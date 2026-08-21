Git Repository Clone Failure over HTTP/2

​

**Issue Description**



​After resolving the sudo privilege escalation issue and re-running the installation script, the Ansible task failed while pulling down the repository source code.



**​Error Output:**



*\[ERROR]: Task failed: Module failed: Cloning into '/home/node/tpotce'...*

*error: RPC failed; curl 92 HTTP/2 stream 5 was not closed cleanly: CANCEL (err 8)*

*error: 2742 bytes of body are still expected*

*fetch-pack: unexpected disconnect while reading sideband packet*

*fatal: early EOF*

*fatal: fetch-pack: invalid index-pack output*

*Origin: /home/node/tpot.yml:785:7*



*783       - "Ubuntu"*

*784* 

*785   - name: Clone / Update T-Pot repository (All)*

&#x20;      *^ column 7*



*fatal: \[127.0.0.1]: FAILED! => {"changed": false, ... "msg": "Cloning into '/home/node/tpotce'...\\nerror: RPC failed; curl 92 HTTP/2 stream 5 was not closed cleanly: CANCEL (err 8)..."}*





**Root Cause Analysis**



​Network instability and HTTP/2 stream resets (curl 92) caused large git object packs to terminate prematurely during the clone process over HTTPS.



**​Fix \& Verification Steps**

​Configured Git globally to force HTTP/1.1 protocols, increased the buffer size, cleaned existing directories, and performed a shallow clone (--depth 1):



*# Clean failed partial directory*

*rm -rf \~/tpotce*



*# Enforce HTTP/1.1 and increase buffer size to 500MB*

*git config --global http.version HTTP/1.1*

*git config --global http.postBuffer 524288000*



*# Verify remote access*

*git ls-remote \[https://github.com/telekom-security/tpotce.git](https://github.com/telekom-security/tpotce.git) HEAD*



*# Perform depth-1 shallow clone*

*git clone --depth 1 \[https://github.com/telekom-security/tpotce.git](https://github.com/telekom-security/tpotce.git) \~/tpotce*



*# Launch installation directly from local source*

*cd \~/tpotce*

*./install.sh*



**Result:** Git successfully cloned the repository (1659 objects, 65.86 MiB). The playbook executed smoothly with ok=42 changed=12 failed=0.





