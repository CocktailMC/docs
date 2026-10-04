# Pull Request

## 打开之前

1. 分支基于该仓库的默认分支（ESPlus 为 `master`）。
2. 按 [测试](testing.md) 跑过与改动对应的命令，并把结果写进 PR。
3. 用户可见的行为变化同步改该仓库的 README 或架构文档。跨仓库约定有变时，再改本 `docs` 仓库。

## 标题与正文

标题和提交说明同一风格：`type: 做了什么`。

正文固定四段，没有的写「无」：

```text
## 改动
-

## 原因
-

## 验证
- 命令：
- 结果：

## 风险
- 许可 / ABI / 配置迁移 / 无
```

界面改动附上管理端或游戏内的前后说明。只贴一张静态图不够，写明点过的路径。

## 规模

一个 PR 对应一个可回滚的变更。混在一起时拆开：

- 重构与行为修复分开
- 依赖大版本升级单独开 PR
- 文档仓库的流程修改可以一个 PR 覆盖多篇，但不要顺手改 Cocktail 源码

## 合并

满足 [代码评审](review.md) 的条件后合并进默认分支。

合并方式：

- 普通功能：Squash merge，保留一条进默认分支的说明。
- 已经是一组整理过的提交、且每条都要留在历史上：Merge commit。

不使用 rebase merge 把未经同意的提交者邮箱改写进默认分支。

## DT 计数

[versioning.md](../versioning.md) 规定 Cocktail 每合并 2 个有效 PR 生成一个新的 DT 主版本。有效 PR 指改变控制面、管理端、插件或打包行为的合并。只改 Markdown、`design/`、`docs/`、`.github/` 或 logo 的 PR 不计数。要排除一次代码合并，给 PR 加上 `dt:skip` 或 `release:skip`。

合并进 `main` 之后由发版工作流判断，不在 PR 里手工发 DT。计数的实现见 [automation.md](automation.md)。
