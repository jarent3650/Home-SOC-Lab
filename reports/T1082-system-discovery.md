## Test Results

| Test # | Technique | Executed? | Wazuh alert? |
|---|---|---|---|
| 1 | systeminfo / reg query | Yes | No |
| 7 | Hostname Discovery | Yes | No |
| 9 | MachineGUID Discovery | Yes | No |
| 11 | Environment variables | Yes | No |
| 27 | WMIC system info | Yes | No |
| 29 | Check computer location | Yes | No |
| 30 | BIOS via Registry | Yes | No |
| 34 | OS discovery (CimInstance) | Yes | No |
| 35 | OS version via ver | Yes | No |
| 36 | Volume shadow copies (vssadmin) | Yes | No |
| 37 | Locale/Regional settings | Yes | No |
| 38 | Enumerate drives | Yes | No |
| 39 | OS Product Name via Registry | Yes | No |
| 40 | OS Build Number via Registry | Yes | No |
| 41 | Hardware UUID via wmic | Yes | No |

**Note:** Wazuh did log 34 alerts during this window, but all were unrelated to the tests — a coincidental scheduled CIS/SCA benchmark scan (rules 19005, 19009, 19012, 19013), routine Windows Defender signature-update tasks (60642, 60608), and background service/VSS events (61104, 61102, 60702). None matched the timing or nature of any T1082 command.

## Key Finding
T1082 (System Information Discovery) achieved **0% detection coverage** across 14 tested sub-techniques using Wazuh's default Windows Security log monitoring. This is expected: these commands are local, read-only operations (registry queries, environment variable reads, command-line system info tools) that Windows does not log to the Security event channel by default. Detecting this technique would require either Sysmon (for process-creation visibility) or a command-line auditing policy enabling 4688 with command-line logging.
