**SSH Connection Refused on Original IP (192.168.1.199)**



​**Issue Description**



​Upon completion of the installer script, the terminal displayed the standard T-Pot re-configuration summary:



*Text:*

*### Done. Please reboot and re-connect via SSH on tcp/64295.*



Attempting to connect back to the server using the designated command:

&#x09;*ssh -p 64295 node@192.168.1.199*



**resulted in the error:**

&#x09;*ssh: connect to host 192.168.1.199 port 64295: Connection refused*





**Root Cause Analysis**

​Two factors caused this connection refusal:

1. Service Relocation \& Reboot Requirement: T-Pot relocates the host's real SSH administrative daemon from default port 22 to custom port 64295 so that honeypots (like Cowrie) can listen on port 22. The relocated SSH service and Docker containers require a full system reboot to start listening.
2. ​Dynamic IP Change (DHCP Lease Renewal): During the post-install interface setup and network system restart, the local DHCP router re-assigned the host VM a new IP address (192.168.1.200) instead of preserving 192.168.1.199. Connecting to .199 targeted an offline/unassigned endpoint.

​

**Fix \& Verification Steps**



​**Step A:** Port Diagnostic via PowerShell

​Executed network diagnostic probes from the host machine to verify port states and identify the new host address:



*# Check standard port 22 on new IP address (Expect FALSE - moved to honeypot)*

*Test-NetConnection 192.168.1.200 -Port 22*



*# Check relocated management port 64295 on new IP address (Expect TRUE)*

*Test-NetConnection 192.168.1.200 -Port 64295*



**Diagnostic Output:**



*PS C:\\Users\\i\_Node> Test-NetConnection 192.168.1.200 -Port 22*

*WARNING: TCP connect to (192.168.1.200 : 22) failed*

*TcpTestSucceeded : False*



*PS C:\\Users\\i\_Node> Test-NetConnection 192.168.1.200 -Port 64295*

*ComputerName      : 192.168.1.200*

*RemotePort        : 64295*

*TcpTestSucceeded : True*



**Step B: Re-establishing Administrative Access**

​Connected via SSH using the updated IP address and relocated port:



&#x09;*ssh -p 64295 node@192.168.1.200*



**Result:** Successfully authenticated and accepted new SSH ED25519 host key fingerprint.









