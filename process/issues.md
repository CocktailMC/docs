# Issue

在对应仓库开 Issue，不在 `docs` 里跟踪产品缺陷。`docs` 只收手册自身的错误和缺页。

| 问题落在 | 仓库 |
|---|---|
| 控制面、管理端、打包、WASM 插件 | Cocktail |
| sudo、审计、面板、NeoForge 模组 | ESPlus |
| ABI、引擎、桥、leafmod | leaf |
| 本手册与版本规范 | docs |

跨仓库问题（例如 Cocktail 插件装不上当前 ES+ jar）开在先失败的那一侧，正文链接另一个仓库的相关 Issue。

## 标题

`区域: 一句话现象`

示例：`panel: 默认密码时进程仍监听 8088`、`abi: greeter.leafmod 在 Api 槽位变更后崩溃`。

## 缺陷正文

```text
## 环境
- 仓库与提交：
- 版本或构建号：
- 操作系统：
- Minecraft / 加载器（如有）：

## 步骤
1.

## 实际
-

## 期望
-
```

Cocktail 附上 `cocktail-control --version` 的完整输出（规范格式见 [versioning.md](../versioning.md) 第 16 节）。在该命令尚未打印 CalVer 之前，附上二进制文件名、git 提交和启动日志里的版本行。

ESPlus 附上 `mod_version`、NeoForge 版本、面板是否独立 JVM 启动成功。不要粘贴 `security.db`、私钥或面板密码。

leaf 附上 `LeafApi` 版本、加载器（Fabric / NeoForge / Forge）、是离线 harness 还是 live `runServer`。

## 功能请求

写使用者和现有行为的缺口。实现方案可以提议，评审时允许换成更小的做法。

## 标签

仓库里还没有统一标签集。新建时只用这些：

| 标签 | 用途 |
|---|---|
| `bug` | 与文档或测试不符的行为 |
| `enhancement` | 新能力 |
| `docs` | 仅文档 |
| `security` | 安全，细节见下节 |
| `good first issue` | 边界清楚、不碰密钥与 ABI |

## 安全类 Issue

可公开的加固（缺少校验、文档提醒）用 `security` 标签。

可利用的漏洞不要写利用步骤。请按 [安全](security.md) 私下联系维护者，公开 Issue 只留「存在安全问题，细节已私下同步」和影响范围（哪个仓库、是否默认配置可触发）。
