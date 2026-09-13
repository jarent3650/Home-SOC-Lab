# Home-SOC-Lab
Project Summary
I built this lab to get hands-on experience across the full detection lifecycle: standing up a SIEM, generating realistic attack traffic in a controlled environment, triaging the resulting alerts, and writing custom detection rules where out-of-the-box coverage fell short. The goal was to simulate the actual workflow of a SOC analyst and security engineer rather than just reading about one.

Tools Used
VirtualBox	Virtualization / lab hosting
Wazuh	SIEM — log collection, alerting, dashboard
Kali Linux	Attack simulation platform
Atomic Red Team	Safe, MITRE ATT&CK-mapped attack execution
MITRE ATT&CK	Framework for mapping and categorizing techniques

Attack Sims
T1110	Brute Force	
T1059	Command & Scripting Interpreter	
T1105	Ingress Tool Transfer	
T1547	Boot/Autostart Persistence

Architecture
Wazuh Manager (Ubuntu VM) — central SIEM/XDR platform, dashboard, and alerting
Windows 10 victim (Wazuh agent installed) — target for host-based attack simulation
Ubuntu victim (Wazuh agent installed) — target for Linux-based attack simulation
Kali Linux — attacker machine used to generate malicious activity
All machines isolated on a host-only VirtualBox network — no traffic leaves the lab
