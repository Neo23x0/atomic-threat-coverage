| Title        | Suspicious Process Start Locations |
|:-------------------|:------------------|
| Description        | Detects suspicious process run from unusual locations |
| Tags               |   |
| ATT&amp;CK Tactic | ('Defense Evasion', 'TA0005')  |
| ATT&amp;CK Technique | T1036  |
| Dataneeded         | DN_0002_windows_process_creation_with_commandline_4688, DN_0003_windows_sysmon_process_creation_1 |
| Triggering         | T1036 |
| Severity Level     | medium       |
| False Positives    | False positives depend on scripts and administrative tools used in the monitored environment |
| Development Status | experimental      |
| References         | https://car.mitre.org/wiki/CAR-2013-05-002 |
| Author             | juju4      |


## Detection Rules

### Sigma rule

```
action: global
title: Suspicious Process Start Locations
description: Detects suspicious process run from unusual locations
status: experimental
references:
    - https://car.mitre.org/wiki/CAR-2013-05-002
author: juju4
tags:
    - attack.defense_evasion
    - attack.t1036
detection:
    selection:
        CommandLine:
            - "*:\\RECYCLER\\*"
            - "*:\\SystemVolumeInformation\\*"
            - "%windir%\\Tasks\\*"
            - "%systemroot%\\debug\\*"
    condition: selection
falsepositives: 
    - False positives depend on scripts and administrative tools used in the monitored environment
level: medium
---
# Windows Audit Log
logsource:
    product: windows
    service: security
    description: 'Requirements: Audit Policy : Detailed Tracking > Audit Process creation, Group Policy : Administrative Templates\System\Audit Process Creation'
detection:
    selection:
        EventID: 4688
---
# Sysmon
logsource:
    product: windows
    service: sysmon
detection:
    selection:
        EventID: 1

```





### Kibana query

```
(EventID:"4688" AND CommandLine:("*\\:\\\\RECYCLER\\*" "*\\:\\\\SystemVolumeInformation\\*" "%windir%\\\\Tasks\\*" "%systemroot%\\\\debug\\*"))\n(EventID:"1" AND CommandLine:("*\\:\\\\RECYCLER\\*" "*\\:\\\\SystemVolumeInformation\\*" "%windir%\\\\Tasks\\*" "%systemroot%\\\\debug\\*"))
```





### X-Pack Watcher

```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_xpack/watcher/watch/Suspicious-Process-Start-Locations <<EOF\n{\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "query_string": {\n              "query": "(EventID:\\"4688\\" AND CommandLine:(\\"*\\\\:\\\\\\\\RECYCLER\\\\*\\" \\"*\\\\:\\\\\\\\SystemVolumeInformation\\\\*\\" \\"%windir%\\\\\\\\Tasks\\\\*\\" \\"%systemroot%\\\\\\\\debug\\\\*\\"))",\n              "analyze_wildcard": true\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": null,\n        "subject": "Sigma Rule \'Suspicious Process Start Locations\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\ncurl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_xpack/watcher/watch/Suspicious-Process-Start-Locations-2 <<EOF\n{\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "query_string": {\n              "query": "(EventID:\\"1\\" AND CommandLine:(\\"*\\\\:\\\\\\\\RECYCLER\\\\*\\" \\"*\\\\:\\\\\\\\SystemVolumeInformation\\\\*\\" \\"%windir%\\\\\\\\Tasks\\\\*\\" \\"%systemroot%\\\\\\\\debug\\\\*\\"))",\n              "analyze_wildcard": true\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": null,\n        "subject": "Sigma Rule \'Suspicious Process Start Locations\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```





### Graylog

```
(EventID:"4688" AND CommandLine:("*\\:\\\\RECYCLER\\*" "*\\:\\\\SystemVolumeInformation\\*" "%windir%\\\\Tasks\\*" "%systemroot%\\\\debug\\*"))\n(EventID:"1" AND CommandLine:("*\\:\\\\RECYCLER\\*" "*\\:\\\\SystemVolumeInformation\\*" "%windir%\\\\Tasks\\*" "%systemroot%\\\\debug\\*"))
```

