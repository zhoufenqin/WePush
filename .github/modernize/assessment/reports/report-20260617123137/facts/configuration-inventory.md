# Configuration & Externalized Settings Inventory

WePush uses a single Hutool `Setting` file (`config.setting`) stored in the user's home directory as its sole configuration source, with no Spring environment, application.properties, or externalized config server.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|---|---|---|---|
| `config.setting` | Hutool INI-style key-value file | `~/.WePush5/config/config.setting` | Primary runtime configuration; created on first launch; read/written at runtime via `ConfigUtil` / `ConfigBaseUtil` |
| `mybatis-config.xml` | MyBatis XML configuration | `src/main/resources/mybatis-config.xml` | ORM configuration; JDBC URL injected at runtime via `Properties` object in `MybatisUtil` |
| `logback.xml` | Logback XML configuration | `src/main/resources/logback.xml` | Logging configuration; log files written to `~/.WePush5/logs/` |
| `pom.xml` | Maven build descriptor | `pom.xml` | Dependency management, build plugins, and version properties |
| `db_init.sql` | SQL schema init script | `src/main/resources/db_init.sql` | Executed once on first launch to create all SQLite tables |
| `TAccount.accountConfig` (runtime) | JSON blob in SQLite column | `~/.WePush5/WePush.db` — `t_account.account_config` | Per-channel account credentials stored as JSON; parsed at send time |
| `version_summary.json` | JSON | `src/main/resources/version_summary.json` | Changelog / release notes displayed in-app |
| `.travis.yml` | CI configuration | `.travis.yml` | Travis CI build definition (local JAR install + `mvn clean package`) |

No Spring Boot, Spring Cloud Config, Kubernetes ConfigMap, external secret store (Vault, KeyVault, AWS Secrets Manager), or `.env` file is used.

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---|---|---|---|
| Default (no profile) | Always active | Standard build producing executable JAR | `javapackager` 1.7.5 (main class: `com.fangxuele.tool.push.App`), `maven-install-plugin` 2.5.2, `mybatis-generator-maven-plugin` 1.3.6 |
| Local JAR pre-install | Manual (`install_local_jar_to_repo.bat` / Travis `before_install`) | Installs vendored JARs (`antlr`, `chardet`, `cpdetector`, `jargs`, `taobao-sdk-java-auto`) into local Maven repository before build | `mvn install:install-file` for 5 JARs under `./lib/` and `./src/main/lib/` |

No Maven `<profiles>` blocks are declared in `pom.xml`. The only build variation is whether the local JARs have been pre-installed.

## Runtime Profiles

WePush is a desktop application with no framework profile system (no Spring profiles, no `ASPNETCORE_ENVIRONMENT`). All runtime behaviour is driven by values in `config.setting`.

| Profile | Activation Method | Config Files | Key Overrides |
|---|---|---|---|
| Default (single profile) | Always active | `~/.WePush5/config/config.setting` | All settings read from this file; no environment-based switching |

The `MybatisUtil` class conditionally uses SQLite (always) or MySQL (when `setting.mysql.url` is non-empty) at startup, but this is controlled by a user-set property rather than a deployment profile.

## Properties Inventory

All properties are stored in Hutool `Setting` format: `group.key = value`. The file is at `~/.WePush5/config/config.setting`.

**Section: `[msg]`**

| Property Key | Default | Type | Notes |
|---|---|---|---|
| `msg.msgType` | `13` | int | Currently selected message type code |
| `msg.msgName` | `""` | String | Currently selected message name |

**Section: `[member]`**

| Property Key | Default | Type | Notes |
|---|---|---|---|
| `member.sql` | `""` | String | Custom SQL for importing recipients from MySQL |

**Section: `[push]`**

| Property Key | Default | Type | Notes |
|---|---|---|---|
| `push.dryRun` | `true` | boolean | Whether push runs in dry-run (test) mode |

**Section: `[setting.normal]`**

| Property Key | Default | Type | Notes |
|---|---|---|---|
| `setting.normal.autoCheckUpdate` | `true` | boolean | Auto-check for app updates on launch |
| `setting.normal.useTray` | `true` | boolean | Show system tray icon |
| `setting.normal.closeToTray` | `true` | boolean | Minimize to tray on window close |
| `setting.normal.defaultMaxWindow` | `true` | boolean | Maximize window on launch |
| `setting.normal.unifiedBackground` | `true` | boolean | Use unified background colour |
| `setting.normal.maxThreads` | `100` | int | Maximum parallel push threads; also governs OkHttp connection pool size |
| `setting.normal.pushTotal` | `0` | long | Cumulative push count counter |
| `setting.normal.beforeVersion` | `"v_0.0.0"` | String | Last-seen version (used by upgrade logic) |

**Section: `[setting.mail]`** *(sensitive — see Secrets section)*

| Property Key | Default | Type | Notes |
|---|---|---|---|
| `setting.mail.mailHost` | `"smtp.163.com"` | String | SMTP server hostname |
| `setting.mail.mailPort` | `"25"` | String | SMTP server port |
| `setting.mail.mailFrom` | `""` | String | Sender email address |
| `setting.mail.mailUser` | `""` | String | SMTP authentication username |
| `setting.mail.mailPassword` | `""` | String | SMTP password (plaintext) |
| `setting.mail.mailUseStartTLS` | `false` | boolean | Enable STARTTLS |
| `setting.mail.mailUseSSL` | `false` | boolean | Enable SSL/TLS |

**Section: `[setting.mysql]`** *(sensitive — see Secrets section)*

| Property Key | Default | Type | Notes |
|---|---|---|---|
| `setting.mysql.url` | `""` | String | MySQL JDBC URL suffix (appended to `jdbc:mysql://`) |
| `setting.mysql.user` | `""` | String | MySQL username |
| `setting.mysql.password` | `""` | String | MySQL password (plaintext) |

**Section: `[setting.appearance]`**

| Property Key | Default (Windows/Mac/Linux) | Type | Notes |
|---|---|---|---|
| `setting.appearance.theme` | `"Flat macOS Dark"` | String | FlatLaf theme name |
| `setting.appearance.font` | OS-specific (`微软雅黑` / `PingFang SC` / `Noto Sans CJK HK`) | String | UI font family |
| `setting.appearance.fontSize` | `12` | int | UI font size in points |

## Startup Parameters & Resource Requirements

WePush is launched as a standard Java desktop application. No containerisation, Kubernetes, or cloud deployment manifests exist.

| Service | JVM/Runtime Options | Memory | Notes |
|---|---|---|---|
| WePush Desktop App | No standard JVM flags defined in project; the `.travis.yml` build uses default JVM settings | Not specified | The `readme.md` mentions `-Xms64m -Xmx256m` as example launch parameters for macOS; no official JVM tuning file is committed |
| Logback log files | N/A | N/A | Written to `~/.WePush5/logs/wechat-push.YYYY-MM-DD.log`; time-based rolling; no size limit configured |

The `App.java` macOS launch comment shows: `-Xms64m -Xmx256m -Dapple.awt.application.name="WePush"` as example flags, but these are not enforced by the project build.

## Startup Dependency Chain

WePush is a single-process desktop application with no inter-service dependencies. The startup sequence within the process is:

1. **Theme initialisation** (`Init.initTheme()`) — FlatLaf theme applied before any UI is shown
2. **MainFrame creation and display** — Splash/loading panel shown immediately
3. **`MybatisUtil` static initialiser** — SQLite file checked; if absent, `db_init.sql` is executed to create tables; MyBatis `SqlSessionFactory` built
4. **`ConfigUtil` initialiser** — `config.setting` file loaded (created if absent)
5. **`UpgradeUtil.smoothUpgrade()`** — Version-based schema migration DDL executed
6. **`MainWindow` and tab initialisation** — UI fully rendered
7. **Scheduled task reload** (`TaskListener.addAllScheduledTask()`) — All persisted Quartz jobs re-submitted to the scheduler (runs in background thread)

No readiness probes, health checks, Docker Compose `depends_on`, or `dockerize` wait mechanisms are used.

## Secrets & Sensitive Configuration

All secrets are stored **unencrypted** in the local `config.setting` file or in the SQLite `t_account.account_config` JSON column.

| Secret Reference | Type | Storage |
|---|---|---|
| `setting.mail.mailPassword` | SMTP password | Plaintext in `~/.WePush5/config/config.setting` |
| `setting.mysql.password` | MySQL password | Plaintext in `~/.WePush5/config/config.setting` |
| `t_account.account_config` → AppId/AppSecret | WeChat MP/MA/CP credentials | Plaintext JSON in SQLite `~/.WePush5/WePush.db` |
| `t_account.account_config` → API keys | Alibaba/Tencent/Huawei/Qiniu/YunPian keys | Plaintext JSON in SQLite `~/.WePush5/WePush.db` |
| `t_account.account_config` → webhookUrl | DingTalk bot webhook URL (contains secret token) | Plaintext JSON in SQLite `~/.WePush5/WePush.db` |

### Secrets Provisioning Workflow

WePush has no automated secrets provisioning workflow. Secrets are entered manually by the user via the Settings dialog and Account Management forms within the desktop application, then persisted immediately to the local SQLite database or `config.setting` file. There is no secrets rotation, no encryption at rest, no integration with HashiCorp Vault, Azure KeyVault, or AWS Secrets Manager, and no environment-variable injection mechanism. The security boundary is the operating-system file-permission model on the user's local machine.

## Feature Flags

WePush does not use a dedicated feature-flag framework (no LaunchDarkly, Unleash, Spring Feature Flags, or `.NET FeatureManagement`). The following settings act as informal feature toggles:

| Flag Name | Default | Controlled By | Behaviour |
|---|---|---|---|
| `push.dryRun` | `true` | `config.setting` + UI checkbox | When `true`, send logic executes but no actual API call is made; results logged as successful |
| `setting.normal.autoCheckUpdate` | `true` | `config.setting` + Settings dialog | Enables automatic version-check HTTP request on application startup |
| `setting.normal.useTray` | `true` | `config.setting` + Settings dialog | Enables system tray icon and tray-based minimise |
| `setting.normal.closeToTray` | `true` | `config.setting` + Settings dialog | Minimises to tray instead of exiting when window is closed |
| `setting.normal.unifiedBackground` | `true` | `config.setting` + Settings dialog | Applies a unified background colour to the main window |

## Framework & Runtime Versions

| Component | Version | Source |
|---|---|---|
| Java (target) | 21 | `pom.xml` `<java.version>21</java.version>` |
| Java (Travis CI build) | OpenJDK 8 | `.travis.yml` `jdk: openjdk8` (outdated — mismatches pom.xml target) |
| Maven (build tool) | Not pinned (system Maven) | `.travis.yml` — no `mvn wrapper` or `<maven.version>` enforced |
| MyBatis | 3.5.13 | `pom.xml` |
| FlatLaf (UI) | 3.5.2 | `pom.xml` |
| Quartz Scheduler | 2.3.2 | `pom.xml` |
| WeiXin Java SDK (MP/MA/CP) | 4.6.0 | `pom.xml` |
| Hutool | 5.6.4 | `pom.xml` |
| Logback | 1.2.3 | `pom.xml` |
| OkHttp | 4.9.1 | `pom.xml` |
| Fastjson | 1.2.74 | `pom.xml` |
| Lombok | 1.18.30 | `pom.xml` |
| SQLite JDBC | 3.43.0.0 | `pom.xml` |
| MySQL Connector/J | 5.1.47 | `pom.xml` |
| javapackager Maven plugin | 1.7.5 | `pom.xml` |
| mybatis-generator-maven-plugin | 1.3.6 | `pom.xml` |
| Velocity Engine | 2.3 | `pom.xml` |
| Apache POI (OOXML) | 4.1.2 | `pom.xml` |
