* **T-Pot Honeypot Cybersecurity Lab**



A practical home cybersecurity lab built with T-Pot, Ubuntu Server, Docker, VirtualBox, and a Windows 11 host. A honeypot is a safe, fake system designed to lure attackers away from real networks. It logs every action an intruder takes without risking actual company data. In this lab, we deploy Cowrie, a popular low-interaction SSH honeypot that logs brute-force password attempts and shell commands.



This project documents the installation, configuration, network architecture, services, troubleshooting, monitoring, and lessons learned while deploying a T-Pot honeypot in a local virtualized environment.



The purpose of this lab is to safely study:



1. Honeypot technology
2. Network reconnaissance
3. Attack traffic
4. SSH attacks
5. Web attacks
6. Brute-force activity
7. Malware and exploit attempts
8. Network monitoring
9. Docker-based security services
10. T-Pot Attack Map
11. Log collection and analysis
12. Incident investigation
13. Cybersecurity documentation



**Lab Safety**



> This project is intended for an authorized home/lab environment.

> Do not expose a honeypot directly to the Internet unless you understand the security implications and have properly isolated the system.



\--------------------------------------------------------------------------------------------------------------------



* **Table of Contents**



\- \[Lab Overview](#-lab-overview)

\- \[Objectives](#-objectives)

\- \[Infrastructure](#-infrastructure)

\- \[Network Architecture](#-network-architecture)

\- \[IP Addressing](#-ip-addressing)

\- \[T-Pot Services](#-t-pot-services)

\- \[Ports](#-ports)

\- \[Prerequisites](#-prerequisites)

\- \[VirtualBox Configuration](#-virtualbox-configuration)

\- \[T-Pot Installation](#-t-pot-installation)

\- \[Starting T-Pot](#-starting-t-pot)

\- \[Checking T-Pot Status](#-checking-t-pot-status)

\- \[Accessing SSH](#-accessing-ssh)

\- \[Accessing the Web Interface](#-accessing-the-web-interface)

\- \[Docker Verification](#-docker-verification)

\- \[Network Verification](#-network-verification)

\- \[Troubleshooting](#-troubleshooting)

\- \[Problem: T-pot-installation-playbook-run-into-issue](#-The Ansible playbook execution failed almost immediately during the bootstrapping phase.)

\- \[Problem: T-pot-playbook-run-into-issue-cloning-issue-again](#-Git-Repository-Clone-Failure-over-HTTP/2)

\- \[Problem: SSH Connection Refused](#-problem-ssh-connection-refused)

\- \[Useful Commands](#-useful-commands)

\- \[Logs](#-logs)

\- \[Monitoring](#-monitoring)

\- \[Security Considerations](#-security-considerations)

\- \[Lessons Learned](#-lessons-learned)

\- \[Future Improvements](#-future-improvements)

\- \[Project Structure](#-project-structure)



\----------------------------------------------------------------------------------------------------------------------



* **Lab Overview**



This laboratory uses a Windows 11 computer as the physical host.



VirtualBox runs an Ubuntu Server virtual machine containing the T-Pot honeypot platform.



The T-Pot VM is connected to the same local network as the Windows host.



**Main Components**



| Component | Description |

|---|---|

| Windows 11 | Physical host / management workstation |

| VirtualBox | Virtualization platform |

| Ubuntu Server | T-Pot operating system |

| T-Pot | Honeypot platform |

| Docker | Container platform used by T-Pot |

| SSH | Remote administration |

| T-Pot Web UI | Honeypot monitoring interface |

| T-Pot Attack Map | Visualization of attack activity |



\------------------------------------------------------------------------------------------------------------------------



* **Objectives**



The main objectives of this project are:



1\. Deploy a functional T-Pot honeypot.

2\. Run T-Pot inside an Ubuntu Server VM.

3\. Manage the VM remotely through SSH.

4\. Access the T-Pot web interface from Windows.

5\. Monitor Docker containers.

6\. Investigate network connectivity problems.

7\. Troubleshoot SSH connectivity.

8\. Troubleshoot T-Pot web interface problems.

9\. Monitor honeypot services.

10\. Collect and analyze attack logs.

11\. Document troubleshooting procedures.

12\. Build a portfolio-ready cybersecurity project.



\-----------------------------------------------------------------------------------------------------------------------



* **Infrastructure**



Physical Host



The physical computer runs:



**text**

Windows 11

&#x20;     │

&#x20;     ├── VirtualBox

&#x20;     │

&#x20;     └── Ubuntu Server VM

&#x20;             │

&#x20;             └── T-Pot



\---------------------------------------------------------------------------------------------------------------------



**Nigeria Threat Context \& Security Strategy**

1. **Core Insight: The Visibility Gap:** Traditional threat telemetry (such as T-Pot honeypots) routinely fails to detect Nigerian and West African threat actors. This absence reflects an operational shift rather than a lack of activity. These groups rely on human-operated social engineering, proxy infrastructure (EU/US residential VPNs), and targeted identity compromise rather than automated port scanning.
2. **Primary Attack Vectors:** Business Email Compromise (BEC) \& Domain Spoofing: Direct targeting of financial, HR, and executive personnel to execute unauthorized wire transfers and account takeovers.
3. **Fintech \& Mobile API Exploitation:** Directly targeting local payment gateways (Flutterwave, Paystack, OPay) and leveraging SIM swapping to bypass standard SMS-based authentication.
4. **Document-Delivered RATs:** Stealth delivery of AsyncRAT, Remcos, and similar payloads hidden inside lure documents sent via email, WhatsApp, or Telegram.



**Strategic Hardening Priorities**



1. **Identity First:** Eliminate SMS MFA in favor of FIDO2 hardware tokens to defeat SIM swapping. Enforce DMARC p=reject and institute out-of-band verification for all external wire transfers.
2. **Access \& Endpoint Control:** Apply strict egress filtering to restrict unauthorized C2 traffic. Lock down exposed database services (Elasticsearch/Redis) behind TLS and RBAC, disable legacy protocols (SMBv1), and maintain a strict 30-day patching SLA.
3. **Visibility \& Governance:** Log DNS queries and endpoint executions to catch stealth malware, align retention with regulatory standards (e.g., Nigerian Data Protection Regulation / ICO guidelines), and run targeted anti-phishing drills for finance and HR teams.



\----------------------------------------------------------------------------------------------------------------------



**Key Takeaways/Lessons Learned**



1. Network Architecture is Foundational: Deploying T-Pot reinforced that proper network planning is as critical as honeypot configuration. Hosting the Ubuntu server via VirtualBox on the local network enabled smooth host-to-guest communication and ensured a controlled environment.
2. Visibility Drives Defense: You cannot defend what you cannot see. Without continuous monitoring, malicious behavior goes undetected; T-Pot bridges this gap by exposing controlled services to generate actionable threat intelligence.
3. Documentation Validates Expertise: Thorough documentation transforms a technical setup into a proof of capability, showcasing practical troubleshooting, analytical rigor, and incident-response skills.



\------------------------------------------------------------------------------------------------------------------------





Thanks to Mr Imran Hassan, Nexus Space and 3MTT, for the skill and knowledge during the programme training.



I am Ige Taiye Ezekiel, this is my Capstone project on Honeypot Analysis.



