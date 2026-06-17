# Dependency Map

WePush is a Java 21 desktop push-notification tool with approximately 35 declared external dependencies spanning messaging SDKs, GUI frameworks, data access, scheduling, and utilities.

## Dependencies

```mermaid
flowchart LR
    App["WePush v5.0.5"]

    subgraph Messaging["Messaging / Push SDKs"]
        WxMP["weixin-java-mp v4.6.0"]
        WxMA["weixin-java-miniapp v4.6.0"]
        WxCP["weixin-java-cp v4.6.0"]
        JavaMail["javax.mail v1.6.2"]
        QCloud["qcloudsms v1.0.6"]
        YunPian["yunpian-java-sdk v1.2.7"]
        TxCloud["tencentcloud-sdk-java v3.1.847"]
    end
    subgraph CloudSDK["Cloud Provider SDKs"]
        AliCore["aliyun-java-sdk-core v4.4.2"]
        AliSMS["aliyun-java-sdk-dysmsapi v1.1.0"]
        BdCloud["bce-java-sdk v0.10.134"]
        Qiniu["qiniu-java-sdk v7.3.0"]
    end
    subgraph DB["Database / ORM"]
        MyBatis["mybatis v3.5.13"]
        SQLite["sqlite-jdbc v3.43.0"]
        MySQL["mysql-connector-java v5.1.47"]
    end
    subgraph Sched["Scheduling"]
        Quartz["quartz v2.3.2"]
    end
    subgraph GUI["GUI Framework"]
        FlatLaf["flatlaf v3.5.2"]
        FlatExtras["flatlaf-extras v3.5.2"]
        FlatThemes["flatlaf-intellij-themes v3.5.2"]
        FormsRT["forms-rt v7.0.3"]
    end
    subgraph Log["Logging"]
        Logback["logback-classic v1.2.3"]
    end
    subgraph Util["Utilities"]
        Hutool["hutool-all v5.6.4"]
        Lombok["lombok v1.18.30"]
        Fastjson["fastjson v1.2.74"]
        OkHttp["okhttp v4.9.1"]
        HttpAsync["httpasyncclient v4.1.4"]
        Velocity["velocity-engine-core v2.3"]
        POI["poi-ooxml v4.1.2"]
        OpenCSV["opencsv v5.2"]
        JsonPath["json-path v2.6.0"]
        EmojiJava["emoji-java v5.1.1"]
        CPDetector["cpdetector (local)"]
    end

    App -->|"messaging SDKs"| Messaging
    App -->|"cloud APIs"| CloudSDK
    App -->|"persistence"| DB
    App -->|"scheduling"| Sched
    App -->|"desktop UI"| GUI
    App -->|"logging"| Log
    App -->|"utilities"| Util
```

### Dependency Summary

| Category | Count | Key Libraries | Notes |
|---|---|---|---|
| Messaging / Push SDKs | 7 | weixin-java-mp/miniapp/cp 4.6.0, javax.mail 1.6.2, tencentcloud-sdk-java 3.1.847 | Core value-add libraries; WeiXin SDK 4.6.0 is current |
| Cloud Provider SDKs | 4 | aliyun-java-sdk-core 4.4.2, bce-java-sdk 0.10.134, qiniu-java-sdk 7.3.0 | Each SDK pins its own transitive HTTP client versions |
| Database / ORM | 3 | mybatis 3.5.13, sqlite-jdbc 3.43.0.0, mysql-connector-java 5.1.47 | MySQL connector is on the old 5.x branch |
| Scheduling | 1 | quartz 2.3.2 | Quartz 2.3.x is in maintenance mode |
| GUI Framework | 4 | flatlaf 3.5.2, forms-rt 7.0.3 | FlatLaf is actively maintained; forms-rt is IntelliJ-specific |
| Logging | 1 | logback-classic 1.2.3 | Declared as `provided` scope |
| Utilities | 11 | hutool-all 5.6.4, fastjson 1.2.74, okhttp 4.9.1, poi-ooxml 4.1.2 | Several outdated versions (see risks below) |

### Version & Compatibility Risks

**mysql-connector-java 5.1.47** is on the legacy GPL 5.x driver line, which reached end-of-active-development; the current connector is 8.x (Connector/J). **fastjson 1.2.74** has multiple known CVEs in the 1.x series (arbitrary code execution via auto-type deserialization); the library was superseded by fastjson2. **logback-classic 1.2.3** predates the CVE-2021-42550 fix (patched in 1.2.9+) and the newer 1.5.x/2.x releases. **httpasyncclient 4.1.4** depends on the legacy Apache HttpComponents 4.x stack, which is in maintenance mode. **quartz 2.3.2** (2019) is the last stable release on the 2.x branch with no planned 3.x successor, limiting cloud-native scheduling migration options. **HikariCP** is defined in `<properties>` at version 3.4.5 but its `<dependency>` block is commented out, meaning the connection pool is either pulled transitively or not used at runtime.

### Notable Observations

- **Overlapping HTTP client libraries**: The project simultaneously declares `okhttp`, `httpasyncclient` (Apache HC4), and each WeChat/Cloud SDK brings its own HTTP client transitively, leading to multiple competing HTTP stacks on the classpath.
- **Local/vendored JARs**: `cpdetector`, `antlr`, `chardet`, `jargs`, and `taobao-sdk-java-auto` are declared via `<scope>system</scope>` with paths under `src/main/lib`, which is incompatible with standard Maven build pipelines and CI/CD.
- **fastjson 1.x security risk**: `fastjson 1.2.74` is in the 1.x series known for critical deserialization CVEs. Upgrading to fastjson2 or Jackson requires API changes throughout the codebase.
- **No connection-pool JAR declared**: HikariCP is referenced in source code (`HikariUtil.java`) but its Maven dependency is commented out; the pool may arrive only as a transitive dependency of another library.

## Test Dependencies

| Framework | Version | Notes |
|---|---|---|
| JUnit | 4.13.1 | Classic JUnit 4 (test scope); no migration to JUnit 5 detected |

Total test-scope dependencies: 1

Only a single test dependency (JUnit 4) is declared. There is no mocking library (Mockito), assertion library (AssertJ/Hamcrest beyond built-ins), or integration test framework detected. Test coverage is therefore expected to be minimal for this desktop application.
