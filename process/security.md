# 安全

## 报告漏洞

把可利用的问题私下发给仓库维护者（GitHub Security Advisories，仓库启用后走 Advisory；未启用时私下联系组织所有者）。

公开内容只包括：受影响仓库、版本范围、是否默认配置可触发。利用步骤、口令样本、内存布局留在私下渠道，修复发布后再写公告。

## 各仓库的敏感数据

| 数据 | 放在哪 | 禁止 |
|---|---|---|
| Cocktail 管理员口令 | `data/cocktail.db`（argon2） | 提交数据库、在日志打印口令 |
| Cocktail 机器令牌 | 环境变量 `COCKTAIL_API_TOKEN` | 写入前端仓库或截图 |
| ES+ 面板密码 | 面板 JVM 环境变量 | `application-runtime.properties`、JVM argv、审计明文 |
| ES+ sudo 口令 | BCrypt，再经 AES-256-GCM，主密钥由 RSA-2048 OAEP 包装 | 提交 `config/esplus/keys/` 或 `security.db` |
| ES+ 默认口令 `esplus` | 仅作为拒绝启动的哨兵 | `panelAllowDefaultPassword = true` 的生产配置 |
| leaf | 无内置账号 | 把映射缓存或玩家存档提交进仓库 |

ES+ 面板绑定地址保持 `127.0.0.1`。远程访问用 SSH 转发或 `deploy/public-ingress/`。Cocktail 控制面默认可以绑定 `0.0.0.0:11011`，暴露到局域网之外时必须已经完成管理员初始化，并限制来源网络。

## 开发时

- 测试账号用本地临时目录，测完删除 `data/` 与 `esplus/`。
- 崩溃 webhook、代理地址用自己的端点。PR 里不留真实 URL 上的令牌。
- 依赖升级若修复已知漏洞，在 PR 里写漏洞编号和影响面。
- ESPlus 的 LGPL 与 Cocktail / leaf 的 Apache-2.0 不要在同一次提交里混抄许可头。

## 修复流程

1. 私下确认影响版本。
2. 在默认分支修复，测试见 [testing.md](testing.md)。
3. 已有 GA 或已公开的 ES+ 版本时，按 [release.md](release.md) 发 HF（Cocktail）或新的 SemVer 补丁（ESPlus），不覆盖旧资产。
4. 公告写影响范围、修复版本、用户要改的配置。不写完整利用链，除非维护者决定需要用户自行缓解且没有别的办法描述。

## 威胁模型摘要

- Cocktail：本机控制面。认证是单一最高管理员加可选机器令牌，还没有 RBAC。评审时不要假设多租户隔离已经存在。
- ESPlus：威胁来自已经是 OP 的账号和被盗的面板口令。门禁和审计是默认能力；升权必须经过面板角色。
- leaf：威胁来自恶意或错位的原生模组。ABI 与 handle 是边界。桥接代码不要扩大成第二个加载器。
