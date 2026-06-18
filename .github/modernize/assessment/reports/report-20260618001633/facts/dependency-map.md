# Dependency Map

This Maven-based Java desktop application declares roughly thirty-five direct dependencies spanning desktop UI, local persistence, cloud messaging SDKs, and utility libraries. The dependency mix is integration-heavy because a single executable supports many outbound messaging channels.

## Dependencies

```mermaid
flowchart LR
    App["WePush application"]

    subgraph UI["UI and Desktop"]
        FlatLaf["FlatLaf 3.5.2"]
        FlatExtras["FlatLaf Extras 3.5.2"]
        FlatThemes["FlatLaf IntelliJ Themes 3.5.2"]
        FormsRt["forms_rt 7.0.3"]
        AppleExt["AppleJavaExtensions 1.4"]
    end
    subgraph Data["Database and ORM"]
        MyBatis["MyBatis 3.5.13"]
        SQLite["sqlite-jdbc 3.43.0.0"]
        MySQL["mysql-connector-java 5.1.47"]
    end
    subgraph Messaging["Messaging and Provider SDKs"]
        WxMp["weixin-java-mp 4.6.0"]
        WxCp["weixin-java-cp 4.6.0"]
        WxMa["weixin-java-miniapp 4.6.0"]
        AliSms["aliyun dysmsapi 1.1.0"]
        TxSms["tencentcloud-sdk-java 3.1.847"]
        QCloud["qcloudsms 1.0.6"]
        Baidu["bce-java-sdk 0.10.134"]
        Qiniu["qiniu-java-sdk 7.3.0"]
        Yunpian["yunpian-java-sdk 1.2.7"]
        Mail["javax.mail 1.6.2"]
    end
    subgraph Http["HTTP and Serialization"]
        OkHttp["okhttp 4.9.1"]
        HttpAsync["httpasyncclient 4.1.4"]
        Fastjson["fastjson 1.2.74"]
        JsonPath["json-path 2.6.0"]
    end
    subgraph Utility["Utilities and File Processing"]
        Hutool["hutool-all 5.6.4"]
        OpenCsv["opencsv 5.2"]
        Poi["poi-ooxml 4.1.2"]
        Velocity["velocity-engine-core 2.3"]
        Emoji["emoji-java 5.1.1"]
        Quartz["quartz 2.3.2"]
        Charset["cpdetector stack 1.0.x"]
        Lombok["lombok 1.18.30"]
    end
    subgraph Logging["Logging"]
        Logback["logback-classic 1.2.3"]
    end

    App -->|"desktop UI"| UI
    App -->|"persistence"| Data
    App -->|"provider APIs"| Messaging
    App -->|"network and JSON"| Http
    App -->|"helpers"| Utility
    App -->|"logging"| Logging
    WxMp -.->|"dependency exclusions"| HttpAsync
    Yunpian -.->|"dependency exclusions"| HttpAsync
    Baidu -.->|"large exclusion set"| Logback
```

### Dependency Summary

| Category | Count | Key Libraries | Notes |
|---|---:|---|---|
| UI and Desktop | 5 | FlatLaf, forms_rt, AppleJavaExtensions | Desktop look-and-feel and installer packaging support |
| Database and ORM | 3 | MyBatis, sqlite-jdbc, mysql-connector-java | Local SQLite is primary; MySQL import support is also configured |
| Messaging and Provider SDKs | 10 | WeChat SDKs, Aliyun, Tencent, Baidu, Qiniu, Yunpian, JavaMail | Largest category because one executable serves many delivery channels |
| HTTP and Serialization | 4 | OkHttp, HttpAsyncClient, fastjson, json-path | Used for provider integrations and HTTP push mode |
| Logging | 1 | Logback | Central logging implementation |
| Utilities | 8 | Hutool, OpenCSV, POI, Quartz, Velocity, cpdetector, Lombok | File import, scheduling, templating, and general helper functionality |

### Version & Compatibility Risks

Several declared dependencies are dated relative to the Java 21 target level. `mysql-connector-java` 5.1.47 is end-of-life, `logback-classic` 1.2.3 predates current security and maintenance updates, `hutool-all` 5.6.4 and `httpasyncclient` 4.1.4 are older utility/network baselines, and `fastjson` 1.2.74 is especially notable because it has a long history of security concerns and migration pressure toward safer JSON libraries.

### Notable Observations

- The repository relies on a wide set of provider SDKs instead of isolating each channel into a separate module, so dependency upgrades can have broad blast radius.
- Multiple dependencies declare explicit exclusions, especially the Baidu SDK, indicating ongoing transitive dependency conflict management.
- The build uses local `lib/*.jar` installation steps for Taobao and cpdetector artifacts, which also explains why baseline Maven resolution is sensitive to repository and clean-phase behavior.
- Local desktop concerns and cloud-provider integrations coexist in one artifact, making the dependency graph unusually diverse for a single-process app.

## Test Dependencies

| Framework | Version | Notes |
|---|---:|---|
| JUnit | 4.13.1 | Legacy unit-test framework used by the existing `CommonTest` class |

Total test-scope dependencies: 1

Test infrastructure is minimal and focused on basic local checks rather than layered unit, integration, or contract testing. No mocking, integration-container, or UI-test libraries are declared in `pom.xml`.
