# CocktailMC 文档

CocktailMC 的组织级文档：各仓库做什么、怎么协作、怎么发版。

实现细节留在各自仓库。这里只记录跨仓库都要遵守的事实和流程。

## 仓库

| 仓库 | 默认分支 | 语言 | 许可 | 角色 |
|---|---|---|---|---|
| [Cocktail](https://github.com/CocktailMC/Cocktail) | `main` | Rust、React | Apache-2.0 | 单机多实例 Minecraft 控制面 |
| [ESPlus](https://github.com/CocktailMC/ESPlus) | `master` | Java、Spring Boot | LGPL-3.0-or-later | NeoForge 1.21.1 安全套件 |
| [leaf](https://github.com/CocktailMC/leaf) | `main` | C++23、Kotlin/Native | Apache-2.0 | 跨加载器原生模组运行时 |
| [docs](https://github.com/CocktailMC/docs) | `main` | Markdown | 尚未单独声明 | 本手册 |

项目说明见 [projects/README.md](projects/README.md)。

## 开发流程

| 文档 | 内容 |
|---|---|
| [工具链](process/toolchain.md) | 各仓库的编译器和本地启动命令 |
| [Git](process/git.md) | 分支、提交、默认分支差异 |
| [Pull Request](process/pull-requests.md) | 从开分支到合并 |
| [代码评审](process/review.md) | 评审关注点和合并条件 |
| [Issue](process/issues.md) | 缺陷、功能和安全报告 |
| [测试](process/testing.md) | 合并前必须跑过的检查 |
| [发行](process/release.md) | 阶段晋级、制品、各仓库版本差异 |
| [Cocktail 自动化](process/automation.md) | CI、DT 计数和人工发版 |
| [安全](process/security.md) | 密钥、面板、漏洞处理 |
| [文档维护](process/documentation.md) | 什么写在这里，什么留在源码仓库 |

版本号规范已单独成文：[versioning.md](versioning.md)。

## 阅读顺序

1. 先看 [projects/README.md](projects/README.md)，确认要改的是哪一个仓库。
2. 按 [process/toolchain.md](process/toolchain.md) 把本地环境跑起来。
3. 按 [process/git.md](process/git.md) 和 [process/pull-requests.md](process/pull-requests.md) 提交改动。
4. 发版前读 [versioning.md](versioning.md) 和 [process/release.md](process/release.md)。
