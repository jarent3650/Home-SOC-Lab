# SOC Home Lab: Detection & Threat Simulation

A self-built Security Operations Center lab. I deployed a SIEM (Wazuh), connected endpoint agents, ran MITRE ATT&CK-mapped attack simulations with Atomic Red Team, and documented what was detected, what was blocked, and what was missed.

**Status:** In progress. This README is updated as each phase is completed. See [Roadmap](#roadmap).

## Project Summary

I built this lab to get hands-on experience with the detection lifecycle: standing up a SIEM, generating controlled attack activity, triaging the resulting alerts, and writing up findings the way I would in a SOC. Everything runs in VirtualBox on an isolated network, and nothing is exposed to the internet.

## Lab Architecture

| Machine | Role | OS | Status |
|---|---|---|---|
| Wazuh Manager | SIEM / alerting / dashboard | Wazuh OVA (Linux) | Running |
| Windows-Victim | Monitored endpoint, attack target | Windows 11 Enterprise Evaluation | Wazuh agent active |
| Ubuntu-Victim | Monitored Linux endpoint, SSH target | Ubuntu Server | Wazuh agent active |
| Kali Linux | Attacker machine | Kali Linux | Attacker, reachable from the victim VMs |

**Networking:** All VMs share a VirtualBox Host-Only network (isolated from my home network). The Windows VM has a second NAT adapter used only to download tools; lab traffic runs over the Host-Only adapter.

## Tools Used

| Tool | Purpose |
|---|---|
| VirtualBox | Virtualization |
| Wazuh | SIEM: log collection, rules, alerting, dashboard |
| Atomic Red Team | Safe, MITRE ATT&CK-mapped attack simulation |
| Kali Linux | Attack platform (planned: SSH brute force) |
| Windows Defender | Endpoint protection on the victim (observed blocking attacks) |
| MITRE ATT&CK | Technique mapping |
| GitHub | Documentation and version history |

## Attack Simulations & Results

| MITRE ATT&CK ID | Technique | Result | Report |
|---|---|---|---|
| T1136.001 | Create Local Account (test 4, via command prompt) | Detected by Wazuh (Windows event 4720) | [Report](reports/T1136.001-create-account.md) |
| T1110 | Brute Force (SSH, Kali to Ubuntu) | Planned | [Report](reports/T1110-brute-force-attack.md) |
| T1059.001 | PowerShell: Mimikatz (test 1) | **Prevented**: Windows Defender blocked execution before it ran | Planned |
| T1082 | System Information Discovery (full test set) | Mostly executed; Defender blocked one script; several sub-tests failed on missing prerequisites or deprecated tools (e.g. `wmic`) | Planned |


Each report covers what was run, the evidence (alerts and screenshots), my severity assessment, and recommended response.

### Key findings so far

- **Layered defense is visible.** Defender stopped Mimikatz outright, while a low-noise action like creating a local account was allowed to run and had to be caught by the SIEM. Prevention and detection cover different things.
- **Running every sub-test is noisy.** `Invoke-AtomicTest` without `-TestNumbers` ran dozens of variants of T1082. Targeting individual tests gives cleaner evidence and clearer alert-to-action mapping.
- **Detection depends on log sources.** Wazuh alerts only on what Windows logs by default, which motivates adding Sysmon (see Roadmap).

## Troubleshooting & Lessons Learned

Problems I hit and how I resolved them:

- **Windows 11 VM would not boot the install ISO.** Resolved by correcting the VM firmware/boot configuration and moving the ISO to an IDE controller.
- **"This PC doesn't meet Windows 11 requirements" (TPM 2.0).** Bypassed for lab use with the `LabConfig` registry keys during setup.
- **No internet on an isolated VM.** The Host-Only network has no internet by design, so Atomic Red Team failed to download. Fixed by adding a second NAT adapter.
- **Atomic Red Team commands "not recognized."** The module has to be re-imported in each new PowerShell session.
- **Wazuh dashboard API error (HTTP 500).** The manager daemons were running, but systemd had marked the service failed after a slow startup (clock-source warnings suggested CPU starvation on the VM). Verified daemons with `wazuh-control status`, cleared the failed state, confirmed the API responded, and restarted the dashboard.
- **Agent not visible in Wazuh.** Verified agent installation/service state and manager connectivity, then re-ran the test once the agent showed Active.

## Roadmap

- [x] Deploy Wazuh manager and dashboard
- [x] Windows endpoint with active Wazuh agent
- [x] Atomic Red Team installed and tested
- [x] First end-to-end detection (T1136.001)
- [x] Linux endpoint with agent
- [x] SSH brute-force detection from Kali (T1110)
- [ ] Install Sysmon on Windows for richer telemetry
- [ ] Write a custom Wazuh rule for a detection gap
- [ ] Python script to pull alerts via the Wazuh API into a daily summary

## Skills Demonstrated

`SIEM deployment (Wazuh)` `Endpoint agent deployment` `Log analysis` `MITRE ATT&CK mapping` `Incident documentation` `Windows & Linux administration` `VirtualBox networking` `Troubleshooting & root-cause analysis`

## Repo Structure

```
Home-SOC-Lab/
├── README.md
├── diagram/                              (architecture diagram)
├── screenshots/
├── reports/
│   ├── T1136.001-create-account.md
│   ├── T1059.001-mimikatz-blocked.md     (planned)
│   └── T1082-system-discovery.md         (planned)
└── rules/                                (added once custom rules exist)
