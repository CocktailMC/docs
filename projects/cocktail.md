# Cocktail

仓库：<https://github.com/CocktailMC/Cocktail>  
默认分支：`main`  
许可：Apache-2.0  
工作区版本（`Cargo.toml` 的 `workspace.package.version`，2026-10-04）：`26.4.11-DP`

Cocktail Manager 是单机多实例的 Minecraft 控制面。Rust 进程管理本机 Java 或 Docker 容器；React 管理端负责登录、实例和运维。账号存在本机 SQLite。控制面重启后会认回仍在运行的进程或容器。

产品线草案（main / Scroll / Edge / PRO Stable / Enterprise）写在仓库根目录的 `项目方案.md`。二进制身份以 [versioning.md](../versioning.md) 为准：`YYQn.MAJOR.STAGE.MINOR+BUILD`。当前 Cargo 版本字符串还是过渡写法，新发行物按该规范生成，不要再发明第三套号码。

## 目录

```text
Cocktail/
├── crates/cocktail-control      Axum 控制面
├── crates/cocktail-plugin-sdk   Extism 访客 SDK
├── crates/plugins/              cdylib 插件
├── admin/                       React 管理端
├── packaging/                   systemd、env、WiX
├── scripts/                     deb / rpm / msi 打包
├── design/                      界面与发行通道设计稿
└── dotnet/                      相关 .NET 工程
```

工作区成员：

| Crate | 产物 | 作用 |
|---|---|---|
| `cocktail-control` | 默认二进制 | 控制面。`cargo run -p cocktail-control` |
| `cocktail-plugin-sdk` | rlib | 插件客人端。宿主函数在 Extism 默认命名空间，`cocktail_control` 在进程内应答，不走系统代理 |
| `cocktail-plugin-watchdog` | cdylib | 记录崩溃事件，可按配置在崩溃后自动拉起 |
| `cocktail-plugin-speclint` | cdylib | 检查实例 spec：EULA、期望运行态、端口占用 |
| `cocktail-plugin-gameops` | cdylib | 声明式 World / PluginSet / Proxy / Network，由 tick 做 reconcile |
| `cocktail-plugin-esplus` | cdylib | 安装 ES+ jar、写入面板配置、代理面板 |

插件导出 `start`、`http_handle`，按需要再导出 `on_event`、`tick`、`stop`。

`cocktail-plugin-esplus` 源码里的默认下载仓库字符串目前是 `FORGE24/ESPlus`。组织内的规范仓库是 `CocktailMC/ESPlus`。改下载源时改这个常量，并在 PR 里说明。

## 本地运行

需要 Rust stable（edition 2024）和 Node.js 22+。

```bash
cargo run -p cocktail-control
# 管理端，开发时代理 /api
cd admin && npm install && npm run dev
```

- 控制面：`http://127.0.0.1:11011`
- 管理端：`http://127.0.0.1:5173`
- 首次打开管理端会创建最高管理员

环境变量与数据目录以仓库 `README.md` 为准。常用项：`COCKTAIL_BIND`、`COCKTAIL_API_TOKEN`、`COCKTAIL_WEBHOOK_URL`、`COCKTAIL_WEB_ROOT`、`COCKTAIL_PROXY`。

数据在工作目录的 `data/`：`cocktail.db`、`state.json`、`instances/`、`logs/`、`backups/`。

## 打包

Linux：`./scripts/package-linux.sh`，产物在 `dist/`。安装后配置在 `/etc/cocktail/cocktail.env`，数据在 `/var/lib/cocktail`。

Windows：`.\scripts\package-windows.ps1`。便携包数据在 exe 旁的 `data\`；MSI（需要 WiX v3）数据在 `%ProgramData%\Cocktail`。防火墙拉黑和立即踢掉 IPv4 连接需要管理员权限。

## 源码里的设计文档

| 路径 | 内容 |
|---|---|
| `README.md` | 功能表、启动、安装 |
| `项目方案.md` | 2026-09-12 产品线草案 |
| `design/webui/` | 管理端设计 |
| `design/edge-scroll/` | Edge / Scroll 通道设计 |

发行阶段、Build ID、Git tag 与 Release 资产命名见 [versioning.md](../versioning.md)。自动 DT 和人工发版的规则在仓库 `scripts/release/rules.toml`，说明见 [自动化](../process/automation.md)。
