# Configuration & Externalized Settings Inventory

WePush uses a small set of repository-side XML and JSON resources plus a larger amount of user-specific runtime configuration stored under the local user home directory. There are no framework runtime profiles in the server-side sense; instead, feature behavior is mostly controlled through persisted desktop settings and packaging-time Maven plugin configuration.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|---|---|---|---|
| Maven build descriptor | XML | `pom.xml` | Declares Java version, dependencies, and packaging plugins |
| User runtime settings | Hutool setting file | `<user-home>/.WePush5/config/config.setting` | Stores message defaults, mail settings, MySQL import settings, UI preferences, and feature toggles |
| MyBatis configuration | XML | `src/main/resources/mybatis-config.xml` | Defines SQLite JDBC datasource and mapper registrations |
| Logging configuration | XML | `src/main/resources/logback.xml` | Configures console and rolling file logging under the user home directory |
| Database bootstrap | SQL | `src/main/resources/db_init.sql` | Creates initial SQLite schema |
| Incremental database upgrades | SQL | `src/main/resources/upgrade/*.sql` | Applied by `UpgradeUtil` during startup |
| MyBatis generator config | XML | `src/main/resources/generatorConfig.xml` | Supports mapper or domain generation during development |
| Version metadata | JSON | `src/main/resources/version_summary.json` | Tracks release index and changelog details for update UX |
| Theme resources | JSON | `src/main/resources/theme/*.theme.json` | Additional visual themes for the desktop UI |
| Embedded help assets | HTML and images | `src/main/resources/page/` | Local help content shipped with the app |

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---|---|---|---|
| Default Maven build | Default | Compile the Java 21 desktop application JAR | Maven compiler defaults from `pom.xml` |
| `bundling-for-mac` execution | Runs during `package` | Create macOS distribution artifacts with bundled JRE settings | `io.github.fvarrui:javapackager` 1.7.5 |
| Local dependency install executions | Run during `clean` | Install local JARs for Taobao and cpdetector-related artifacts into the local Maven repository | `maven-install-plugin` 2.5.2 |
| MyBatis generator plugin | Manual plugin invocation | Regenerate mapper or domain artifacts from `generatorConfig.xml` | `mybatis-generator-maven-plugin` 1.3.6 |

## Runtime Profiles

| Profile | Activation Method | Config Files | Key Overrides |
|---|---|---|---|
| Default desktop runtime | Start the application | `config.setting`, `mybatis-config.xml`, `logback.xml` | User-selected message type, theme, fonts, update checks, tray behavior, database paths |
| Optional external MySQL import | Enable and populate MySQL settings in `config.setting` | `config.setting` | User-supplied MySQL URL, user, and password for recipient import workflows |
| Platform-specific UI behavior | Determined from OS detection at runtime | No separate file | Default font family, tray icon handling, macOS menu-bar integration |

## Properties Inventory

| Property Key | Default | Profiles | Source |
|---|---|---|---|
| `msg.msgType` | `13` | Default runtime | `config.setting` via `ConfigUtil` |
| `msg.msgName` | empty | Default runtime | `config.setting` via `ConfigUtil` |
| `member.sql` | empty | Optional MySQL import | `config.setting` via `ConfigUtil` |
| `push.dryRun` | `true` | Default runtime | `config.setting` via `ConfigUtil` |
| `setting.normal.autoCheckUpdate` | `true` | Default runtime | `config.setting` via `ConfigUtil` |
| `setting.normal.useTray` | `true` | Default runtime | `config.setting` via `ConfigUtil` |
| `setting.normal.closeToTray` | `true` | Default runtime | `config.setting` via `ConfigUtil` |
| `setting.normal.defaultMaxWindow` | `true` | Default runtime | `config.setting` via `ConfigUtil` |
| `setting.normal.unifiedBackground` | `true` | Default runtime | `config.setting` via `ConfigUtil` |
| `setting.normal.maxThreads` | `100` | Default runtime | `config.setting` via `ConfigUtil` |
| `setting.normal.pushTotal` | `0` | Default runtime | `config.setting` via `ConfigUtil` |
| `setting.normal.beforeVersion` | `v_0.0.0` | Default runtime | `config.setting` via `ConfigUtil` |
| `setting.mail.mailHost` | `smtp.163.com` | Mail workflows | `config.setting` via `ConfigUtil` |
| `setting.mail.mailPort` | `25` | Mail workflows | `config.setting` via `ConfigUtil` |
| `setting.mail.mailFrom` | empty | Mail workflows | `config.setting` via `ConfigUtil` |
| `setting.mail.mailUser` | empty | Mail workflows | `config.setting` via `ConfigUtil` |
| `setting.mail.mailPassword` | empty | Mail workflows | `config.setting` via `ConfigUtil` |
| `setting.mail.mailUseStartTLS` | `false` | Mail workflows | `config.setting` via `ConfigUtil` |
| `setting.mail.mailUseSSL` | `false` | Mail workflows | `config.setting` via `ConfigUtil` |
| `setting.mysql.url` | empty | Optional MySQL import | `config.setting` via `ConfigUtil` |
| `setting.mysql.user` | empty | Optional MySQL import | `config.setting` via `ConfigUtil` |
| `setting.mysql.password` | empty | Optional MySQL import | `config.setting` via `ConfigUtil` |
| `setting.appearance.theme` | `Flat macOS Dark` | Default runtime | `config.setting` via `ConfigUtil` |
| `setting.appearance.font` | OS-specific fallback | Default runtime | `config.setting` via `ConfigUtil` |
| `setting.appearance.fontSize` | `12` or `13` on macOS | Default runtime | `config.setting` via `ConfigUtil` |
| `fontSizeInit` | empty until first-run guide completes | Default runtime | `config.setting` via `ConfigBaseUtil` |

## Startup Parameters & Resource Requirements

| Service | JVM/Runtime Options | Memory | Instance Count |
|---|---|---|---|
| WePush desktop application | No repository-managed `-Xms` or `-Xmx` options; package plugin bundles a JRE and adds `jdk.crypto.ec,jdk.charsets` modules for mac packaging | Not explicitly declared in repository | Single local process |

## Startup Dependency Chain

1. `App.main()` applies OS-specific properties and creates the main frame.
2. `Init.initTheme()` loads the selected look-and-feel before the main UI is shown.
3. `MybatisUtil` ensures the local config directory and SQLite database exist, then opens the shared session.
4. `UpgradeUtil.smoothUpgrade()` runs `db_init.sql` and any pending `resources/upgrade/*.sql` scripts.
5. `MainWindow.init()` and `Init.initAllTab()` populate the tabbed UI and register listeners.
6. `TaskListener.addAllScheduledTask()` restores saved scheduled jobs after startup.

## Secrets & Sensitive Configuration

| Secret Reference | Type | Storage (masked) |
|---|---|---|
| `setting.mail.mailPassword` | SMTP credential | `config.setting` as `[MASKED]` |
| `setting.mysql.password` | Database credential | `config.setting` as `[MASKED]` |
| `TAccount.accountConfig` payloads | Provider API keys, app secrets, webhook tokens | SQLite `t_account.account_config` as `[MASKED]` |

### Secrets Provisioning Workflow

Secrets are provisioned manually through the desktop UI rather than an external secret manager. A user enters provider credentials or mail/MySQL settings into account or settings forms, and the application persists them in the local SQLite database or the local `config.setting` file under the user home directory. No managed identity, vault integration, environment-variable injection, or secret rotation workflow was detected in the repository.

## Feature Flags

| Flag Name | Default | Controlled By |
|---|---:|---|
| `push.dryRun` | `true` | User preference in `config.setting` |
| `setting.normal.autoCheckUpdate` | `true` | User preference in `config.setting` |
| `setting.normal.useTray` | `true` | User preference in `config.setting` |
| `setting.normal.closeToTray` | `true` | User preference in `config.setting` |
| `setting.normal.defaultMaxWindow` | `true` | User preference in `config.setting` |
| `setting.normal.unifiedBackground` | `true` | User preference in `config.setting` |

## Framework & Runtime Versions

| Component | Version | Source |
|---|---:|---|
| Java language target | 21 | `pom.xml` properties |
| Maven project version | 5.0.5 | `pom.xml` |
| MyBatis | 3.5.13 | `pom.xml` |
| SQLite JDBC | 3.43.0.0 | `pom.xml` |
| FlatLaf | 3.5.2 | `pom.xml` |
| Logback | 1.2.3 | `pom.xml` |
| WeChat SDKs | 4.6.0 | `pom.xml` |
| Hutool | 5.6.4 | `pom.xml` |
| javapackager plugin | 1.7.5 | `pom.xml` |
| mybatis-generator plugin | 1.3.6 | `pom.xml` |
