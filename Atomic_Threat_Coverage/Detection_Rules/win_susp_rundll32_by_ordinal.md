| Title                | Suspicious Call by Ordinal                                                                                                                                                 |
|:---------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Description          | Detects suspicious calls of DLLs in rundll32.dll exports by ordinal                                                                                                                                           |
| ATT&amp;CK Tactic    |  <ul><li>[TA0005: Defense Evasion](https://attack.mitre.org/tactics/TA0005)</li><li>[TA0002: Execution](https://attack.mitre.org/tactics/TA0002)</li></ul>  |
| ATT&amp;CK Technique | <ul><li>[T1085: Rundll32](https://attack.mitre.org/techniques/T1085)</li></ul>  |
| Data Needed          | <ul><li>[DN_0002_4688_windows_process_creation_with_commandline](../Data_Needed/DN_0002_4688_windows_process_creation_with_commandline.md)</li><li>[DN_0003_1_windows_sysmon_process_creation](../Data_Needed/DN_0003_1_windows_sysmon_process_creation.md)</li></ul>  |
| Enrichment           |  Data for this Detection Rule doesn't require any Enrichments.  |
| Trigger              | <ul><li>[T1085: Rundll32](../Triggers/T1085.md)</li></ul>  |
| Severity Level       | high |
| False Positives      | <ul><li>False positives depend on scripts and administrative tools used in the monitored environment</li><li>Windows contol panel elements have been identified as source (mmc)</li></ul>  |
| Development Status   | experimental |
| References           | <ul><li>[https://techtalk.pcmatic.com/2017/11/30/running-dll-files-malware-analysis/](https://techtalk.pcmatic.com/2017/11/30/running-dll-files-malware-analysis/)</li><li>[https://github.com/Neo23x0/DLLRunner](https://github.com/Neo23x0/DLLRunner)</li><li>[https://twitter.com/cyb3rops/status/1186631731543236608](https://twitter.com/cyb3rops/status/1186631731543236608)</li></ul>  |
| Author               | Florian Roth |


## Detection Rules

### Sigma rule

```
title: Suspicious Call by Ordinal
id: e79a9e79-eb72-4e78-a628-0e7e8f59e89c
description: Detects suspicious calls of DLLs in rundll32.dll exports by ordinal
status: experimental
references:
    - https://techtalk.pcmatic.com/2017/11/30/running-dll-files-malware-analysis/
    - https://github.com/Neo23x0/DLLRunner
    - https://twitter.com/cyb3rops/status/1186631731543236608
tags:
    - attack.defense_evasion
    - attack.execution
    - attack.t1085
author: Florian Roth
date: 2019/10/22
logsource:
    category: process_creation
    product: windows
detection:
    selection:
        CommandLine: '*\rundll32.exe *,#*'
    condition: selection
falsepositives:
    - False positives depend on scripts and administrative tools used in the monitored environment
    - Windows contol panel elements have been identified as source (mmc)
level: high

```





### splunk
    
```
CommandLine="*\\\\rundll32.exe *,#*"
```






### Saved Search for Splunk

```
Generated with Sigma2SplunkAlert
[Suspicious Call by Ordinal]
action.email = 1
action.email.subject.alert = Splunk Alert: $name$
action.email.to = test@test.de
action.email.message.alert = Splunk Alert $name$ triggered \
List of interesting fields:   \
title: Suspicious Call by Ordinal status: experimental \
description: Detects suspicious calls of DLLs in rundll32.dll exports by ordinal \
references: ['https://techtalk.pcmatic.com/2017/11/30/running-dll-files-malware-analysis/', 'https://github.com/Neo23x0/DLLRunner', 'https://twitter.com/cyb3rops/status/1186631731543236608'] \
tags: ['attack.defense_evasion', 'attack.execution', 'attack.t1085'] \
author: Florian Roth \
date:  \
falsepositives: ['False positives depend on scripts and administrative tools used in the monitored environment', 'Windows contol panel elements have been identified as source (mmc)'] \
level: high
action.email.useNSSubject = 1
alert.severity = 1
alert.suppress = 0
alert.track = 1
alert.expires = 24h
counttype = number of events
cron_schedule = */10 * * * *
allow_skew = 50%
schedule_window = auto
description = Detects suspicious calls of DLLs in rundll32.dll exports by ordinal
dispatch.earliest_time = -10m
dispatch.latest_time = now
enableSched = 1
quantity = 0
relation = greater than
request.ui_dispatch_app = sigma_hunting_app
request.ui_dispatch_view = search
search = CommandLine="*\\rundll32.exe *,#*" | stats values(*) AS * by _time | search NOT [| inputlookup Suspicious_Call_by_Ordinal_whitelist.csv] | collect index=threat-hunting marker="sigma_tag=attack.defense_evasion,sigma_tag=attack.execution,sigma_tag=attack.t1085,level=high"
```