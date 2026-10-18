# Home-SOC-Lab
A self-built Security Operations Center lab. I deployed a SIEM (Wazuh), connected endpoint agents, ran MITRE ATT&CK-mapped attack simulations with Atomic Red Team, and documented what was detected, what was blocked, and what was missed.

Status: In progress. This README is updated as each phase is completed. See Roadmap.

Project Summary

I built this lab to get hands-on experience with the detection lifecycle: standing up a SIEM, generating controlled attack activity, triaging the resulting alerts, and writing up findings the way I would in a SOC. Everything runs in VirtualBox on an isolated network, and nothing is exposed to the internet.

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
