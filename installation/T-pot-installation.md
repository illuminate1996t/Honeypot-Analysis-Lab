Here is the step-by-step breakdown of your T-Pot installation process, highlighting the errors encountered and the specific fixes applied based on the terminal screenshots.



**Step 1: Initial Remote SSH Connection**



Action: Established an SSH connection from Windows PowerShell to the remote Ubuntu server (node@192.168.1.199) on standard port 22.



System Status: Successfully logged in to Ubuntu 26.04 LTS.



**Step 2: First Failure — Privilege Escalation Error (sudo Password Issue)**



Action: Ran the standard T-Pot installer command:



*env bash -c "$(curl -sL https://github.com/telekom-security/tpotce/raw/master/install.sh)"*



Issue Encountered: The Ansible playbook failed during the Get distribution name (All) task.



Error Message: TASK \[Get distribution name (All)] \*\*\* \[ERROR]: Task failed: Premature end of stream waiting for become success.



Root Cause: sudo: interactive authentication is required. The Ansible playbook requires non-interactive sudo rights to run elevated tasks seamlessly, but the user account node prompted for a password.



**Step 3: Fix 1 — Granting Passwordless Sudo Privileges**



Action: Executed commands to allow the node user to run sudo commands without password prompts:



*echo 'node ALL=(ALL) NOPASSWD: ALL' | sudo tee /etc/sudoers.d/tpot-node*

*sudo chmod 440 /etc/sudoers.d/tpot-node*

*sudo visudo -cf /etc/sudoers*



Verification: Confirmed passwordless access worked using sudo -k followed by sudo -n whoami (returned root).



**Step 4: Second Failure — Git HTTP/2 Clone Timeout Error**



Action: Re-ran the automated install script.

Issue Encountered: The Ansible playbook failed at task Clone / Update T-Pot repository (All).



Error Message: error: RPC failed; curl 92 HTTP/2 stream 5 was not closed cleanly: CANCEL (err 8) and fatal: early EOF.



Root Cause: Git encountered network instability/buffering issues over HTTP/2 while cloning the large tpotce repository.



**Step 5: Fix 2 — Git Network Configuration Adjustments \& Manual Shallow Clone**



Action: Tweaked global Git configurations to use HTTP/1.1 and increased the buffer size, then manually cloned the repository with depth limited to 1:



*rm -rf \~/tpotce*

*git config --global http.version HTTP/1.1*

*git config --global http.postBuffer 524288000*

*git clone --depth 1 https://github.com/telekom-security/tpotce.git \~/tpotce*



Result: Repository cloned successfully (65.86 MiB received).



**Step 6: Executing the Local Installation Script**



Action: Navigated into the cloned directory and launched the installer directly:



*cd \~/tpotce*

*./install.sh*



Selection: The Ansible playbook completed all 42 tasks successfully (ok=42 changed=12 failed=0).



Deployment Type: Prompted to select the T-Pot installation type (e.g., Hive, Sensor, Mini, etc.).



**Step 7: Docker Container Pulls \& Management Port Relocation**



Action: The installer pulled all container images (cowrie, dionaea, kibana, elasticsearch, suricata, etc.) via ghcr.io.



Configuration: T-Pot automatically relocated the host's SSH administration port from port 22 to 64295 to allow honeypots to capture standard port 22 traffic.



**Step 8: Network Re-configuration \& Reboot Verification**



Issue Encountered on Reconnect: Executing ssh -p 64295 node@192.168.1.199 resulted in an immediate ssh: connect to host 192.168.1.199 port 64295: Connection refused error.



Root Cause: The T-Pot installation script modified the system's network configuration, triggering a DHCP lease refresh upon service restart. As a result, the router/DHCP server assigned a new IP address (192.168.1.200) to the Ubuntu server, rendering 192.168.1.199 inactive.



Fix \& Resolution:

Identified the updated host IP (192.168.1.200) via network scanning/console output.

Executed a system reboot (sudo reboot) to allow all containerized T-Pot services and the re-mapped SSH daemon to initialize fully.

Re-established the administrative SSH connection using the updated IP address and relocated port:



*ssh -p 64295 node@192.168.1.200*



**Step 9: Network Connectivity and Port Testing**



Action: Before attempting full SSH re-entry, network tests were conducted from the Windows host to the target IP (192.168.1.200) using PowerShell's Test-NetConnection.

Verification 1 (Standard SSH Port 22): Tested connection to the standard SSH port.



Result: WARNING: TCP connect to (192.168.1.200 : 22) failed. TcpTestSucceeded : False. This confirms that T-Pot successfully relocated the host administration SSH and that port 22 is now closed on the host OS.



Verification 2 (New SSH Management Port 64295): Tested connection to the relocated port.



Result: TcpTestSucceeded : True. This confirms the SSH service on the target has successfully migrated to the new designated management port and is accepting connections.



**Step 10: Successful SSH Re-entry via New Management Port**



Action: Re-established an administrative SSH connection using the correct relocated port (64295) and accepted the new host key fingerprint:



*ssh -p 64295 node@192.168.1.200*



Result: Re-authenticated successfully.



System State: Confirmed Ubuntu 26.04 LTS was active as of Aug 13, 2026. This step validates that the management interface is stable following the system reboot described in Step 8.



**Step 11: Validating T-Pot Systemd Service Status**



Action: Verified the status of the main T-Pot service using the command:



*sudo systemctl status tpot --no-pager*



*if the show inactive*

*run this command:*



*sudo systemctl start tpot*



Analysis:

Status: Active: active (running) since Fri 2026-08-14 15:36:05 UTC. This indicates the service started successfully and has been running without crashing.



CGroup Details: Confirmed that docker compose is actively managing the honeypot containers via the defined YAML file.



Log Output (Tail): The recent log entries confirm individual honeypots are initializing:

&#x09;Honeytrap: Initializing plugins and trapping attacks.

&#x09;Mailoney: SMTP Honeypot (Version 0.1) confirmed as Mail Relay listening on 0.0.0.0:25.



**Step 12: Initializing the T-Pot Web Interface**



Action: Accessed the T-Pot web user interface (WebUI) via a secure browser connection to port 64297 



*(*

*\[https://192.168.1.200:64297](https://192.168.1.200:64297)*

*).*



Verification: The landing page rendered successfully, displaying the standard T-Pot hex logo (honey pot).



Available Interfaces: Confirmed access to critical analyst dashboards and tools including:



&#x09;Attack Map: Visualizing attack origins.

&#x09;Elasticvue \& Kibana: For detailed log exploration and data visualization.

&#x09;Spiderfoot \& SecurityMeter: For OSINT and asset assessment.



**Step 13: Service Validation — Attack Map Access**



Action: Clicked the "Attack Map" link to verify the data visualization layer.



Result: The T-Pot Attack Map interface loaded correctly over HTTPS.



Current Status: The map shows "Idle," indicating that while the system is live and monitoring, no active attack data has been captured and processed within the last hour. The right-hand sidebar successfully loaded the legend for "Service Types" (ADB, DNS, FTP, HTTP, etc.), confirming that the map rendering and service classification engine are operational.







