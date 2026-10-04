# 开发流程

这些流程适用于 CocktailMC 的全部公开仓库。仓库特有的命令在 [工具链](toolchain.md) 和 [测试](testing.md)。

| 步骤 | 文档 |
|---|---|
| 装工具、把程序跑起来 | [toolchain.md](toolchain.md) |
| 分支与提交 | [git.md](git.md) |
| 开 PR、写说明、合并 | [pull-requests.md](pull-requests.md) |
| 别人怎么审 | [review.md](review.md) |
| 记缺陷和需求 | [issues.md](issues.md) |
| 合并前跑什么 | [testing.md](testing.md) |
| 怎么打版本和制品 | [release.md](release.md) |
| 密钥和漏洞 | [security.md](security.md) |
| 文档写在哪 | [documentation.md](documentation.md) |

截至 2026-10-04，各仓库都还没有提交 GitHub Actions workflow。下面写的 CI 门禁是合并前的本地义务，以及 [versioning.md](../versioning.md) 里 DT 自动发布所要求的流水线。流水线落地后，以 workflow 文件为准，并回写 [测试](testing.md)。
