# Cocktail 自动化

可执行的规则和脚本在 Cocktail 仓库，不在本手册里复制一份：

| 文件 | 作用 |
|---|---|
| [`scripts/release/rules.toml`](https://github.com/CocktailMC/Cocktail/blob/main/scripts/release/rules.toml) | DT 门槛、不计数的路径、阶段、制品名 |
| [`scripts/release/cocktail_release.py`](https://github.com/CocktailMC/Cocktail/blob/main/scripts/release/cocktail_release.py) | 判断发不发、生成版本号、重命名制品 |
| [`scripts/release/test_cocktail_release.py`](https://github.com/CocktailMC/Cocktail/blob/main/scripts/release/test_cocktail_release.py) | 规则的单元测试，不访问网络 |
| [`.github/workflows/ci.yml`](https://github.com/CocktailMC/Cocktail/blob/main/.github/workflows/ci.yml) | Pull Request 上的检查 |
| [`.github/workflows/dt-release.yml`](https://github.com/CocktailMC/Cocktail/blob/main/.github/workflows/dt-release.yml) | `main` 上的 DT，以及人工发版 |
| [`.github/workflows/checks.yml`](https://github.com/CocktailMC/Cocktail/blob/main/.github/workflows/checks.yml) | 上面两个工作流共用的检查 |

版本字符串仍以 [versioning.md](../versioning.md) 为准。改门槛或路径时，改 `rules.toml` 并让单元测试通过。不要在工作流 YAML 里另写一套计数。

## 推到 main 时

1. 跑 fmt、clippy、`cocktail-control` 测试、四个 WASM 插件、管理端 lint 和 build。
2. 任一失败则停止，不发版。
3. 统计最近一个规范 tag 之后的有效合并 PR。
4. 少于 2 个则成功结束，不创建 Release。
5. 达到 2 个则把本季度主版本加一，阶段为 `DT`，小版本为 `01`，构建号为 `B` 加 Actions 的 `run_number`。
6. 打 Linux deb、rpm、tar.gz 和 Windows zip。有 WiX 时 Windows 脚本还会打 MSI，但 MSI 不是门禁。
7. 创建 GitHub Release。tag 不带构建号。已存在的 tag 直接失败，不覆盖。

DT、DP、AT、BT、RC 标成 Pre-release。GA、HF、LTS 不是 Pre-release。

## 什么 PR 算数

计入控制面、管理端、插件和打包改动。

全部文件都落在下面这些位置时不计入：

- `*.md`
- `design/**`
- `docs/**`
- `.github/**`
- `logo.png`、`logo.ai`

带标签 `release:skip` 或 `dt:skip` 的 PR 不计入。

`Cargo.toml` 的 `26.4.11-DP` 继续留给 Cargo。产品版本不写回这个字段，因为 `26Q4.01.DT.01` 不是 Cargo 能用的 semver。

## 人工发布

在 Actions 里运行 **DT release**。`confirm` 必须填 `release`，否则工作流在规划前失败。

- `bump=major`：本季度新的主版本。
- `bump=minor`：同一阶段的小版本加一。这个季度还没有该阶段时会失败。

DP 及以后的阶段只能走这条路径。自动路径不会因为日期或 PR 数量把 DT 升到 DP。
