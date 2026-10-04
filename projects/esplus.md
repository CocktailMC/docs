# ESPlus

仓库：<https://github.com/CocktailMC/ESPlus>  
默认分支：`master`（组织内其余仓库用 `main`）  
许可：LGPL-3.0-or-later  
版本：SemVer，写在 `gradle.properties` 的 `mod_version`

ES+ 是 NeoForge 1.21.1 安全套件：OP 密码鉴权、受保护指令门禁、行为审计、物品溯源、异常告警、独立 JVM 的 Web 面板。服务端和客户端都要装同一个 fat jar。

它用 SemVer，不套用 Cocktail 的 `YYQn.MAJOR.STAGE`。配置或权限模型的破坏性变更升 minor 或 major，并在 Release 里写迁移说明。

## 运行形态

Spring Boot 面板在独立 JVM 里跑，通过 SQLite（WAL）和游戏进程交换数据，避免 NeoForge ClassLoader 与 Spring 冲突。面板默认 `http://127.0.0.1:8088/`。

```text
Minecraft（NeoForge）
  Command Gate / Event Hooks / Tick
        │
  SecurityService（会话、加密、权限、审计、告警、事发链）
        │
  esplus/security.db
        │
Spring Boot Panel（另一 JVM）
```

## 目录

```text
ESPlus/
├── src/main/java/com/esplus/    Mod 入口、门禁、审计、网络包
├── panel/                      Spring Boot 子工程
├── native/                     可选 Qt6 密码窗（Win64）
├── deploy/                     公网入口只走隧道 / 反代
└── docs/FixBug.md              历史修复记录
```

面板 fat jar 打进 mod jar 的 `META-INF/esplus/esplus-panel.jar`。分发时只发一个 `esplus-*.jar`，不要把面板单独发出去。

## 构建与安装

需要 JDK 21。

```bat
.\gradlew.bat build
```

产物：`build/libs/esplus-*.jar`。放入 NeoForge 21.1.235+ 的服务端和每个客户端的 `mods/`。

首次启动后编辑 `config/esplus-common.toml`：

- `panelBindAddress` 保持 `127.0.0.1`
- `panelPassword` 换成强密码。默认口令在 `panelAllowDefaultPassword = false` 时拒绝启动面板
- 面板密码经环境变量注入面板 JVM，不写入磁盘 properties，不放进 JVM argv

远程访问走 SSH 本地转发，或 `deploy/public-ingress/` 里的 Caddy 与反向隧道。不要把 `8088` 直接映射到公网。

`/setoppw` 只对 Minecraft OP 开放。默认 SEM 角色 `op` 只有 `sudo.session`，没有 `cmd.*`。要执行受保护指令，先在面板里把用户升为 `moderator` 或 `admin`，再 `/sudo`。

## 和 Cocktail 的边界

ES+ 可以单独装在任意 NeoForge 1.21.1 服务器上。Cocktail 的 `cocktail-plugin-esplus` 只负责安装 jar 和代理面板，不替代本仓库的安全模型。权限、审计库、密钥都在游戏实例目录里：

- `esplus/security.db`
- `config/esplus/keys/`
- `esplus/panel/`
- `logs/spring-panel.log`

更细的模块说明见仓库 `README.md`、`OVERVIEW.md`、`MODRINTH.md`。
