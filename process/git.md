# Git 工作流

## 默认分支

| 仓库 | 默认分支 |
|---|---|
| Cocktail、leaf、docs | `main` |
| ESPlus | `master` |

新仓库使用 `main`。ESPlus 在完成历史引用迁移之前，PR 的基线是 `master`。不要在 ESPlus 上平行维护一个无人合并的 `main`。

## 分支

从最新默认分支拉功能分支：

```text
<type>/<short-topic>
```

`type` 用 `feat`、`fix`、`docs`、`refactor`、`test`、`build`、`ci`。

示例：`fix/panel-default-password`、`feat/leaf-player-air`。

- 一个分支只做一件可独立评审的事。
- 不在默认分支上直接开发。
- 不把 `data/`、`build/`、`dist/`、本地 JDK、密钥、`.kagent` 事件日志提交进去。

## 提交说明

一行标题，必要时空行后写原因。标题写为什么改，而不是罗列文件。

```text
fix: refuse panel start when the default password is still set

The runtime properties file must not become a second copy of the secret.
```

语言可以是中文或英文，同一条提交里不要中英各写一遍标题。

关联 Issue 时在正文写 `Fixes #12` 或 `Refs #12`。安全修复不要在公开提交里贴利用细节，见 [安全](security.md)。

## 同步

功能分支用 rebase 或 merge 跟上默认分支都可以，推送前保证 PR 能快进合并或由 GitHub 合并按钮处理。已经推送并有人在审的分支，不要强推，除非评审者同意。

禁止对 `main` / `master` 做 force push。

## 生成文件

| 仓库 | 要提交 | 不要提交 |
|---|---|---|
| Cocktail | `Cargo.lock` | `target/`、`admin/node_modules/`、`dist/`、`data/` |
| ESPlus | Wrapper、`gradle/wrapper` | `build/`、面板解压目录、`*.db` |
| leaf | 源码与 CMake | `build/`、`dist/`、live 运行目录里的世界存档 |
| docs | Markdown | 编辑器临时文件 |

Cocktail 仓库里的 `.kagent/` 含本地代理符号与事件日志，不属于产品源码。新提交不要再扩大这个目录。
