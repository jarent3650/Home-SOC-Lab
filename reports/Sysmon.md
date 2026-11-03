## Sysmon Before/After Comparison

To evaluate whether Sysmon closes the T1082 visibility gap, four sub-tests were re-run 
after installing Sysmon (SwiftOnSecurity config) and adding it as a Wazuh log source.

| Test | Technique | Before Sysmon | After Sysmon |
|---|---|---|---|
| 27 | WMIC system info | No alert (and failed to execute — wmic deprecated) | Alert: rule 92052, level 4 |
| 36 | vssadmin list shadows | No alert | Alerts: rule 92052 (level 4) + rule 92032 (level 3, "Suspicious Windows cmd shell execution") |
| 11 | Environment variables | No alert | Alert: rule 92052, level 4 |
| 40 | OS Build via Registry | No alert | Alert: rule 92052, level 4 |

**Result: 0 of 4 detected before Sysmon, 4 of 4 detected after.**

## Detection Analysis
With Sysmon installed, every test produced at least one alert — including test 27, which 
failed to execute (`wmic` is deprecated on this Windows build) but still triggered an alert, 
because Sysmon detects the process launch itself (`cmd.exe` spawning the atomic test), not 
just a successful outcome. This means Sysmon provides visibility even into failed or blocked 
attack attempts, which the default Windows Security log did not.

Test 36 (vssadmin) triggered a second, more specific alert (rule 92032) beyond the generic 
"abnormal cmd shell" rule that fired for all four tests. This reflects Sysmon's ruleset 
specifically flagging vssadmin as a known LOLBin associated with ransomware shadow-copy 
deletion, demonstrating that Sysmon doesn't just add volume, it adds technique-specific 
detection logic.

## Caveat
All four alerts stem from the same root cause: Atomic Red Team launches tests via `cmd.exe`, 
and Sysmon flags that shell invocation as "abnormal." In a real environment, this specific 
alert (92052) may be partly an artifact of the testing tool itself rather than the underlying 
commands. The vssadmin-specific alert (92032), however, reflects genuine content-based 
detection and is the stronger evidence of Sysmon's value.

## Key Finding
Sysmon closed 100% of the T1082 visibility gap in this sample (4 of 4 vs. 0 of 4), 
confirming it as the correct next step identified in Detection Gaps. The improvement comes 
from process-creation logging (Sysmon event ID 1), which the default Windows Security 
audit policy does not provide.
