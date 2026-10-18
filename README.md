# Home-SOC-Lab
A self-built Security Operations Center lab. I deployed a SIEM (Wazuh), connected endpoint agents, ran MITRE ATT&CK-mapped attack simulations with Atomic Red Team, and documented what was detected, what was blocked, and what was missed.

Status: In progress. This README is updated as each phase is completed. See Roadmap.

Project Summary

I built this lab to get hands-on experience with the detection lifecycle: standing up a SIEM, generating controlled attack activity, triaging the resulting alerts, and writing up findings the way I would in a SOC. Everything runs in VirtualBox on an isolated network, and nothing is exposed to the internet.

Tools Used
VirtualBox	Virtualization/lab hosting
Wazuh	SIEM — log collection, alerting, dashboard
Kali Linux	Attack simulation platform
Atomic Red Team	Safe, MITRE ATT&CK-mapped attack execution
MITRE ATT&CK	Framework for mapping and categorizing techniques

Architecture
Wazuh Manager (Ubuntu VM) — central SIEM/XDR platform, dashboard, and alerting
Windows 10 victim (Wazuh agent installed) — target for host-based attack simulation
Ubuntu victim (Wazuh agent installed) — target for Linux-based attack simulation
Kali Linux — attacker machine used to generate malicious activity

Network:
All VMs share a VirtualBox Host-Only network (isolated from my home network). The Windows VM has a second NAT adapter used only to download tools; lab traffic runs over the Host-Only adapter.

Attack Sims
T1110	Brute Force	
T1059	Command & Scripting Interpreter	
T1105	Ingress Tool Transfer	
T1547	Boot/Autostart Persistence

Key findings so far
Layered defense is visible. Defender stopped Mimikatz outright, while a low-noise action like creating a local account was allowed to run and had to be caught by the SIEM. Prevention and detection cover different things.
Running every sub-test is noisy. Invoke-AtomicTest without -TestNumbers ran dozens of variants of T1082. Targeting individual tests gives cleaner evidence and clearer alert-to-action mapping.
Detection depends on log sources. Wazuh alerts only on what Windows logs by default, which motivates adding Sysmon (see Roadmap).

Troubleshooting & Lessons Learned

Problems I hit and how I resolved them:

Windows 11 VM would not boot the install ISO. Resolved by correcting the VM firmware/boot configuration and moving the ISO to an IDE controller.
"This PC doesn't meet Windows 11 requirements" (TPM 2.0). Bypassed for lab use with the LabConfig registry keys during setup.
No internet on an isolated VM. The Host-Only network has no internet by design, so Atomic Red Team failed to download. Fixed by adding a second NAT adapter.
Atomic Red Team commands "not recognized." The module has to be re-imported in each new PowerShell session.
Wazuh dashboard API error (HTTP 500). The manager daemons were running, but systemd had marked the service failed after a slow startup (clock-source warnings suggested CPU starvation on the VM). Verified daemons with wazuh-control status, cleared the failed state, confirmed the API responded, and restarted the dashboard.
Agent not visible in Wazuh. Verified agent installation/service state and manager connectivity, then re-ran the test once the agent showed Active

