# WePush

## Summary

| Metric | Value |
|--------|-------|
| Total Issues | 9 |
| Mandatory Blockers | 4 |
| Potential Issues | 1 |

## Component Information

| Property | Value |
|----------|-------|
| Language | Java |
| Frameworks | N/A |
| Build tools | Maven |
| JDK version | 21 |

## Cloud Readiness Issues

| Issue Name | Criticality | Story Points | Occurrences |
|------------|-------------|--------------|-------------|
| Use of unsecured network protocols or URI libraries | Mandatory | 3 | [7](#Use_of_unsecured_network_protocols_or_URI_libraries) |
| CRA: Certificate validation bypass - TrustAllCerts | Mandatory | 8 | [2](#CRA_Certificate_validation_bypass_-_TrustAllCerts) |
| Avoid File System Logging in Configuration | Mandatory | 1 | [2](#Avoid_File_System_Logging_in_Configuration) |
| No Dockerfile found | Mandatory | 3 | 1 |
| MySQL database found | Potential | 5 | [2](#MySQL_database_found) |
| Avoid using hardcoded URLs (HTTP protocol) in source code | Optional | 3 | [31](#Avoid_using_hardcoded_URLs_HTTP_protocol_in_source_code) |
| Quartz Scheduler usage detected | Optional | 13 | [1](#Quartz_Scheduler_usage_detected) |
| SQLite usage detected | Optional | 5 | [1](#SQLite_usage_detected) |

### Issue Details

<details id="Use_of_unsecured_network_protocols_or_URI_libraries">
<summary><b>Use of unsecured network protocols or URI libraries</b> — affected files</summary>

- `src/main/java/com/fangxuele/tool/push/ui/component/TopMenuBar.java (line 226)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/CommonTipsDialog.java (line 71)`
- `src/main/java/com/fangxuele/tool/push/ui/form/msg/MailMsgForm.java (line 72)`
- `src/main/java/com/fangxuele/tool/push/ui/form/msg/MailMsgForm.java (line 242)`
- `src/main/java/com/fangxuele/tool/push/ui/form/account/WxMpAccountForm.java (line 295)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/NewTaskDialog.java (line 215)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/NewTaskDialog.java (line 979)`

</details>

<details id="CRA_Certificate_validation_bypass_-_TrustAllCerts">
<summary><b>CRA: Certificate validation bypass - TrustAllCerts</b> — affected files</summary>

- `src/main/java/com/fangxuele/tool/push/logic/msgsender/HwYunMsgSender.java (line 21)`
- `src/main/java/com/fangxuele/tool/push/logic/msgsender/HwYunMsgSender.java (line 170)`

</details>

<details id="Avoid_File_System_Logging_in_Configuration">
<summary><b>Avoid File System Logging in Configuration</b> — affected files</summary>

- `src/main/resources/logback.xml (line 9)`
- `src/main/resources/logback.xml (line 20)`

</details>

<details id="MySQL_database_found">
<summary><b>MySQL database found</b> — affected files</summary>

- `pom.xml (line 156)`

</details>

<details id="Avoid_using_hardcoded_URLs_HTTP_protocol_in_source_code">
<summary><b>Avoid using hardcoded URLs (HTTP protocol) in source code</b> — affected files</summary>

- `src/main/java/com/fangxuele/tool/push/logic/msgsender/DingMsgSender.java (line 232)`
- `src/main/java/com/fangxuele/tool/push/logic/msgsender/DingMsgSender.java (line 258)`
- `src/main/java/com/fangxuele/tool/push/logic/msgsender/UpYunMsgSender.java (line 32)`
- `src/main/java/com/fangxuele/tool/push/ui/UiConsts.java (line 93)`
- `src/main/java/com/fangxuele/tool/push/ui/UiConsts.java (line 98)`
- `src/main/java/com/fangxuele/tool/push/ui/UiConsts.java (line 103)`
- `src/main/java/com/fangxuele/tool/push/ui/UiConsts.java (line 108)`
- `src/main/java/com/fangxuele/tool/push/ui/UiConsts.java (line 110)`
- `src/main/java/com/fangxuele/tool/push/ui/component/TopMenuBar.java (line 226)`
- `src/main/java/com/fangxuele/tool/push/ui/component/TopMenuBar.java (line 266)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/AboutDialog.java (line 98)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/AboutDialog.java (line 134)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/AboutDialog.java (line 214)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/NewTaskDialog.java (line 160)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/NewTaskDialog.java (line 215)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/NewTaskDialog.java (line 979)`
- `src/main/java/com/fangxuele/tool/push/ui/form/account/WxMpAccountForm.java (line 295)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/UpdateDialog.java (line 84)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/UpdateInfoDialog.java (line 84)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/importway/ImportByDing.java (line 94)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/importway/ImportByDing.java (line 162)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/importway/ImportByDing.java (line 289)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/importway/ImportByDing.java (line 462)`
- `src/main/java/com/fangxuele/tool/push/ui/dialog/importway/ImportByDing.java (line 540)`
- `src/main/java/com/fangxuele/tool/push/ui/form/AboutForm.java (line 123)`
- `src/main/java/com/fangxuele/tool/push/ui/form/msg/MailMsgForm.java (line 72)`
- `src/main/java/com/fangxuele/tool/push/ui/form/msg/MailMsgForm.java (line 242)`
- `src/main/java/com/fangxuele/tool/push/ui/form/msg/DingMsgForm.java (line 103)`
- `src/main/java/com/fangxuele/tool/push/ui/listener/AboutListener.java (line 45)`
- `src/main/java/com/fangxuele/tool/push/ui/listener/AboutListener.java (line 81)`
- `src/main/java/com/fangxuele/tool/push/ui/listener/HelpListener.java (line 28)`

</details>

<details id="Quartz_Scheduler_usage_detected">
<summary><b>Quartz Scheduler usage detected</b> — affected files</summary>

- `pom.xml (line 306)`

</details>

<details id="SQLite_usage_detected">
<summary><b>SQLite usage detected</b> — affected files</summary>

- `pom.xml (line 291)`

</details>

## Upgrade Issues

| Issue Name | Criticality | Story Points | Occurrences |
|------------|-------------|--------------|-------------|
| Java Version is not the latest LTS | Optional | 8 | [2](#Java_Version_is_not_the_latest_LTS) |

### Issue Details

<details id="Java_Version_is_not_the_latest_LTS">
<summary><b>Java Version is not the latest LTS</b> — affected files</summary>

- `pom.xml (line 40)`
- `pom.xml`

</details>

---

## Codebase Insights

> **Note:** These documents are generated by AI and may contain inaccuracies or incomplete information. Please review carefully.

1. **[Architecture Diagram](facts/architecture-diagram.md)** — Understand the big picture: system layers and component relationships
2. **[Dependency Map](facts/dependency-map.md)** — Know what the project depends on and where the risks are
3. **[API & Service Contracts](facts/api-service-contracts.md)** — See how services communicate and what contracts they expose
4. **[Data Architecture](facts/data-architecture.md)** — Explore data models, storage, and data flow patterns
5. **[Configuration Inventory](facts/configuration-inventory.md)** — Review how the application is configured across environments
6. **[Business Workflows](facts/business-workflows.md)** — Trace end-to-end business processes and domain logic

[Share feedback](https://aka.ms/ghcp-appmod/feedback)
