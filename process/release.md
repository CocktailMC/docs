# 发行

Cocktail 的版本字符串、阶段和制品命名以 [versioning.md](../versioning.md) 为唯一规范。本文只规定谁可以发、发之前做什么、三个仓库如何并存。

## 阶段谁来批

| 阶段 | 谁批准 | 条件 |
|---|---|---|
| DT | 流水线；流水线缺失时由维护者手工发 | 每 2 个有效 PR，且 [测试](testing.md) 里的关键检查全绿 |
| DP、AT-D、AT、BT-D、BT、RC-D、RC、GA | 维护者书面决定（PR 或 Release 草稿里写「批准进入某阶段」） | 见 versioning 对应小节。CI 不因日期或 PR 数量自动晋级 |
| HF | 维护者 | 仅安全、数据损坏、无法启动、严重兼容、生产故障 |
| LTS | 维护者单独宣布支持窗口 | 不接收大功能 |

已公开的版本不可覆盖。修复时增加小版本号或主版本号，并使用新的 Build ID。

Git tag 使用 `v26Q4.18.AT-D.03` 这种不带 Build ID 的形式。Build ID 只出现在二进制和文件名里。

## 通道

`项目方案.md` 里的 Scroll / Edge 是产品通道草案。它们和阶段的对应关系：

| 通道草案 | 版本规范中的位置 |
|---|---|
| main 上持续合并 | 源码，不单独发版本 |
| Scroll（滚动、最新可构建） | DT，以及维护者挑选的 DP |
| Edge（更稳的切点） | AT 及之后、GA 之前的公开阶段 |
| 正式推荐 | GA |
| 正式版上的紧急修复 | HF |

在 `cocktail update --channel=` 真的存在之前，通道只用于 Release 说明里的文字，不作为安装器参数承诺。

## Cocktail 制品

Release 标题：`Cocktail Manager 26Q4.18.AT-D.03`  
标签：`v26Q4.18.AT-D.03`

资产名：

```text
Cocktail.26Q4.18.AT-D.03+B1842.Windows.x86_64.zip
Cocktail.26Q4.18.AT-D.03+B1842.Linux.x86_64.tar.gz
Cocktail.26Q4.18.AT-D.03+B1842.Windows.x86_64.msi
Cocktail.26Q4.18.AT-D.03+B1842.Linux.x86_64.deb
Cocktail.26Q4.18.AT-D.03+B1842.Linux.x86_64.rpm
```

打包命令见 [Cocktail 项目页](../projects/cocktail.md)。Linux 用 `scripts/package-linux.sh`，Windows 用 `scripts/package-windows.ps1`。

当前 `Cargo.toml` 工作区版本是 `26.4.11-DP`。下一次改版本时直接换成规范格式，不要同时保留两套字段。

`--version` 目标输出：

```text
Cocktail Manager 26Q4.18.AT-D.03+B1842
Commit: 97940e0
Built: 2026-10-04T08:21:33Z
Channel: AT-D
Target: x86_64-pc-windows-msvc
```

## ESPlus 制品

ESPlus 继续 SemVer。GitHub Release 只上传一个 `esplus-<mod_version>.jar`。面板已内嵌，不另附。

标签：`v<mod_version>`，例如 `v1.2.0`。

Release 正文包含：

- NeoForge 与 Minecraft 版本（当前为 NeoForge 21.1.235+、Minecraft 1.21.1）
- 配置或权限模型是否要迁移
- 默认密码拒绝启动面板、绑定地址保持回环这两条安全说明

Modrinth 或其他镜像使用同一文件。

## leaf 制品

尚无定期 Release。第一次发布时：

- tag 遵循 versioning 规范
- 资产注明目标三元组和 `LeafApi` 版本
- 附上 `scripts/assemble-install.sh` 产生的安装树说明
- ABI 不兼容时升主版本号，并在说明里写「旧 leafmod 不能加载」

## 发版清单

1. 默认分支就是要发的提交。
2. 版本号没有被用过。
3. [测试](testing.md) 的对应命令在目标系统上通过。
4. Release 正文列出变更、已知问题、升级注意。
5. 资产文件名带 Build ID，tag 不带 Build ID。
6. 上传后抽查文件大小与校验和，不替换已上传文件。
