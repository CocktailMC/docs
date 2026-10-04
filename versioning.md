# Cocktail 版本命名与发行生命周期规范

## 1. 目的

本文档定义 Cocktail 项目的版本命名规则、发行阶段、构建编号、小版本编号及版本晋级流程。

该版本体系主要用于解决以下问题：

- 清晰表达版本所属时间周期；
- 区分开发、测试、验收、候选与正式发行阶段；
- 支持高频 CI/CD 构建与自动化测试；
- 允许精确追踪某个二进制产物对应的构建任务；
- 为未来 Scroll、Edge、Stable 等不同发行通道提供统一版本基础；
- 避免仅依靠 Git Commit SHA 识别用户实际安装版本。

本规范采用以下三类思想组合：

**CalVer（Calendar Versioning） + Release Stage（发行阶段） + Build Metadata（构建元数据）**

---

# 2. 标准版本格式

Cocktail 标准版本号格式如下：

`YYQn.MAJOR.STAGE.MINOR+BUILD`

例如：

`26Q4.18.AT-D.03+B1842`

各字段含义如下：

| 字段 | 示例 | 含义 |
|---|---|---|
| YY | `26` | 年份，即 2026 |
| Qn | `Q4` | 当年第几个季度 |
| MAJOR | `18` | 当前季度内主版本序号 |
| STAGE | `AT-D` | 当前发行生命周期阶段 |
| MINOR | `03` | 当前阶段内的小版本序号 |
| BUILD | `B1842` | CI/CD 构建编号 |

因此：

`26Q4.18.AT-D.03+B1842`

表示：

> 2026 年第四季度，第 18 个主版本，处于开发者验收测试阶段，是该阶段第 3 个小版本，由第 1842 次构建流水线产生。

---

# 3. 时间版本

## 3.1 年份

年份使用两位数字：

`26`

表示：

`2026`

---

## 3.2 季度

季度使用：

`Q1`
`Q2`
`Q3`
`Q4`

例如：

`26Q4`

表示：

> 2026 年第四季度版本线。

季度切换后，主版本编号原则上重新开始。

例如：

`26Q4.29.GA.01+B2310`

之后可能进入：

`27Q1.01.DT.01+B2351`

具体起始编号可根据项目需要调整。

---

# 4. 主版本编号

主版本编号表示当前季度中的主要迭代节点。

例如：

`26Q4.18`

其中：

`18`

表示 2026 Q4 的第 18 个版本节点。

主版本编号必须：

- 单调递增；
- 不重复使用；
- 已发布版本号不得重新赋予其他代码；
- 阶段晋级时原则上创建新的主版本编号。

例如不推荐：

`26Q4.18.DT`
→
`26Q4.18.DP`

推荐：

`26Q4.18.DT`
→
`26Q4.19.DP`

这样能够保证：

> 一个版本号唯一对应一个确定的代码状态。

---

# 5. 发行生命周期

Cocktail 的标准发行生命周期为：

`DT → DP → AT-D → AT → BT-D → BT → RC-D → RC → GA`

不同阶段代表不同的软件成熟度与质量要求。

---

## 5.1 DT — Dynamic Testing

**Dynamic Testing / 动态测试版**

DT 是最接近主开发分支的可运行测试版本。

主要用途：

- 验证最新 PR 集成结果；
- 验证主线是否能够正常构建；
- 提前发现不同功能组合产生的问题；
- 为开发者提供最新可执行产物。

DT 可以包含：

- 未完全完成的功能；
- 临时接口；
- 数据结构调整；
- Breaking Change；
- 尚未稳定的实验功能。

DT 不承诺：

- API 稳定；
- 配置兼容；
- 数据库兼容；
- 升级兼容；
- 长期支持。

### DT 自动生成规则

默认规则：

> 每合并 2 个有效 PR，生成一个新的 DT 主版本。

例如：

PR #20 合并  
PR #21 合并

触发：

`26Q4.18.DT.01+B1812`

之后：

PR #22 合并  
PR #23 合并

触发：

`26Q4.19.DT.01+B1824`

若 CI 未通过，则本次 DT 不得发布。

---

# 5.2 DP — Developer Preview

**Developer Preview / 开发者预览版**

DP 是经过人工选择的开发阶段预览版本。

相比 DT，DP 应满足：

- 核心程序能够正常启动；
- 主要功能链路可以工作；
- 不存在已知的大规模数据破坏问题；
- 基础 Windows / Linux 构建通过；
- 核心功能已进行人工验证。

DP 仍允许：

- API 改动；
- UI 大规模调整；
- 数据模型修改；
- 插件 ABI 调整；
- 功能增加与删除。

DP 面向：

- 开发者；
- 插件开发者；
- 早期用户；
- 愿意提交问题反馈的测试用户。

---

# 5.3 AT-D — Acceptance Testing Developer

**Developer Acceptance Testing / 开发者验收测试版**

AT-D 表示项目开始从“功能开发”转向“功能验收”。

这一阶段主要验证：

> 功能是否按照设计目标正确实现。

测试重点包括：

- 功能完整性；
- API 行为；
- 数据结构；
- 权限模型；
- 关键业务流程；
- 安装、升级、迁移；
- Windows / Linux 平台差异。

AT-D 仍允许一定程度的架构调整。

例如：

`26Q4.22.AT-D.01+B1960`

---

# 5.4 AT — Acceptance Testing

**Acceptance Testing / 验收测试版**

AT 表示正式进入功能验收阶段。

此时：

- 主要功能已经完成；
- Feature Scope 基本确定；
- 不再随意加入大型功能；
- 主要工作转为修复验收问题。

AT 阶段原则上允许：

- Bug Fix；
- UI 修正；
- 兼容性修复；
- 性能优化；
- 必要的小范围设计修改。

原则上不再允许：

- 大规模架构重构；
- 非必要 Breaking Change；
- 大型新功能。

---

# 5.5 BT-D — Beta Testing Developer

**Developer Beta Testing / 开发者 Beta 测试版**

BT-D 是进入 Beta 阶段前的开发者稳定性测试。

主要目标：

- 性能；
- 长时间运行；
- 数据迁移；
- 升级；
- 恢复；
- 崩溃恢复；
- 多实例；
- Docker / Podman；
- Agent；
- Windows 网络相关行为。

BT-D 后，功能原则上进入冻结状态。

---

# 5.6 BT — Beta Testing

**Beta Testing / 公开 Beta 测试版**

BT 面向更广泛用户。

进入 BT 后：

> Feature Freeze 正式生效。

原则上只允许：

- Bug Fix；
- Security Fix；
- 性能修复；
- 兼容性修复；
- 文档修复；
- UI/UX 小幅调整。

大型新功能必须延期至下一版本线。

---

# 5.7 RC-D — Release Candidate Developer

**Developer Release Candidate / 开发者发布候选版**

RC-D 用于模拟真正的正式发行。

测试重点：

- Release Pipeline；
- 安装包；
- MSI；
- DEB；
- RPM；
- ZIP；
- 升级路径；
- 数据迁移；
- 回滚；
- Release Notes；
- SBOM；
- 数字签名；
- 完整安装与卸载流程。

RC-D 应尽可能接近最终 GA。

---

# 5.8 RC — Release Candidate

**Release Candidate / 正式发布候选版**

RC 是正式版之前的最后公开候选版本。

进入 RC 后，原则上禁止：

- 新功能；
- 架构重构；
- UI 大改；
- API Breaking Change。

仅接受：

- P0 / P1 Bug；
- 数据损坏问题；
- 安全漏洞；
- 严重兼容性问题；
- 发布阻断问题。

例如：

`26Q4.28.RC.01+B2240`

如果发现问题，可以发布：

`26Q4.28.RC.02+B2252`

也可以升级主版本：

`26Q4.29.RC.01+B2260`

---

# 5.9 GA — General Availability

**General Availability / 正式公开发行版**

GA 是推荐普通用户使用的正式版本。

例如：

`26Q4.30.GA.01+B2300`

GA 应满足：

- 完整 CI 通过；
- Windows 构建通过；
- Linux 构建通过；
- 前端构建通过；
- 核心测试通过；
- 升级测试通过；
- 基础安全测试通过；
- Release Notes 完成；
- 安装包完成；
- 已知严重问题清零。

GA 版本不得被替换。

如果正式版出现问题，应发布新的维护版本，而不是覆盖原有制品。

---

# 6. 小版本编号

版本中的：

`.01`

称为 **Small Version ID / Minor Release ID**。

例如：

`26Q4.18.AT-D.03+B1842`

其中：

`03`

表示：

> 当前主版本和当前发行阶段中的第 3 个可分发修订。

Small Version ID 通常在以下情况下增加：

- 修复 Bug；
- 修改配置；
- 修改安装程序；
- 修复打包问题；
- 修正文档与资源；
- 重新发布可分发版本。

例如：

`26Q4.18.AT-D.01+B1828`

发现问题后：

`26Q4.18.AT-D.02+B1835`

再次修复：

`26Q4.18.AT-D.03+B1842`

---

# 7. Build ID

Build ID 格式：

`+Bxxxx`

例如：

`+B1842`

完整版本：

`26Q4.18.AT-D.03+B1842`

Build ID 应由 CI/CD 自动产生。

推荐来源：

- GitHub Actions `run_number`；
- CI 全局递增构建号；
- 内部统一 Build Sequence。

Build ID 的目标不是表达版本成熟度，而是：

> 精确定位某个实际二进制产物。

同一个源代码版本如果因为打包环境不同发生重新构建，可以出现：

`26Q4.18.AT-D.03+B1842`

以及：

`26Q4.18.AT-D.03+B1843`

二者语义版本相同，但构建身份不同。

---

# 8. Small Version ID 与 Build ID 的区别

必须明确区分：

## Small Version ID

表示：

> 软件发布层面的修订。

例如：

`.03`

用户可以感知。

---

## Build ID

表示：

> CI/CD 产生的具体构建任务。

例如：

`+B1842`

主要用于：

- 调试；
- 日志；
- Bug Report；
- Release Trace；
- 二进制验证。

Build ID 不应该承担软件版本排序职责。

---

# 9. 示例

## 动态测试

`26Q4.12.DT.01+B1520`

---

## 开发者预览

`26Q4.15.DP.01+B1668`

---

## 开发者验收

`26Q4.18.AT-D.03+B1842`

---

## 正式验收

`26Q4.20.AT.01+B1910`

---

## Developer Beta

`26Q4.22.BT-D.02+B2015`

---

## Public Beta

`26Q4.24.BT.01+B2072`

---

## Developer RC

`26Q4.26.RC-D.01+B2160`

---

## Release Candidate

`26Q4.28.RC.02+B2252`

---

## 正式版本

`26Q4.30.GA.01+B2300`

---

# 10. 一条完整发行线示例

一个季度可能形成：

```text
26Q4.11.DP.01+B1401

26Q4.12.DT.01+B1450
26Q4.13.DT.01+B1491
26Q4.14.DT.01+B1532

26Q4.15.DP.01+B1600

26Q4.16.DT.01+B1644
26Q4.17.DT.01+B1701

26Q4.18.AT-D.01+B1755
26Q4.18.AT-D.02+B1772
26Q4.18.AT-D.03+B1790

26Q4.19.AT.01+B1840

26Q4.20.BT-D.01+B1901
26Q4.21.BT.01+B1970

26Q4.22.RC-D.01+B2040

26Q4.23.RC.01+B2101
26Q4.23.RC.02+B2115

26Q4.24.GA.01+B2150
```

---

# 11. 发布阶段与稳定性

从左到右，版本稳定性逐步提高：

```text
DT
↓
DP
↓
AT-D
↓
AT
↓
BT-D
↓
BT
↓
RC-D
↓
RC
↓
GA
```

可以概括为：

| 阶段 | 核心目标 |
|---|---|
| DT | 能跑 |
| DP | 能用 |
| AT-D | 开发者验收 |
| AT | 功能验收 |
| BT-D | 开发稳定 |
| BT | 用户稳定 |
| RC-D | 模拟发行 |
| RC | 正式候选 |
| GA | 正式发行 |

---

# 12. `-D` 后缀

`-D` 表示：

**Developer Channel / Developer Validation**

例如：

`AT-D`

代表：

> AT 阶段中的开发者验证版本。

`BT-D`

代表：

> Beta 阶段中的开发者测试版本。

`RC-D`

代表：

> RC 阶段中的开发者候选版本。

因此：

`AT-D → AT`

`BT-D → BT`

`RC-D → RC`

构成一套统一结构。

---

# 13. 自动化发行规则

## DT

允许自动发布。

触发条件：

> 每合并两个有效 PR。

执行流程：

```text
PR Merge
↓
累计 PR Count
↓
达到 2
↓
生成新 DT Version
↓
Cargo Test
↓
Cargo Clippy
↓
Rustfmt
↓
Frontend Lint
↓
Frontend Build
↓
Windows Build
↓
Linux Build
↓
Package
↓
Upload Artifact
↓
Create DT Release
```

任何关键 CI 失败：

> 不得发布 DT。

---

# 14. 人工发行阶段

以下阶段必须由人工批准：

- DP
- AT-D
- AT
- BT-D
- BT
- RC-D
- RC
- GA

CI 不应仅因为时间或 PR 数量自动晋级这些阶段。

阶段晋级属于：

> Release Management Decision

而不是单纯的：

> Build Automation Decision

---

# 15. 版本不可变原则

任何已经公开发行的版本均视为不可变。

禁止：

- 用不同二进制覆盖同一版本；
- 修改旧 Release Asset 后保持原 Build ID；
- 将旧版本号重新用于新的代码；
- 将不同 Commit 发布为同一个完整版本号。

若需要修复，必须增加：

- Small Version ID；
- 主版本号；
- 或发行新的维护版本。

---

# 16. 二进制版本信息

所有 Cocktail 可执行文件建议支持：

```text
cocktail-control --version
```

输出：

```text
Cocktail Manager 26Q4.18.AT-D.03+B1842
Commit: 97940e0
Built: 2026-10-04T08:21:33Z
Channel: AT-D
Target: x86_64-pc-windows-msvc
```

Web 控制面也应展示：

```text
Version
26Q4.18.AT-D.03

Build
B1842

Commit
97940e0
```

这样 Bug Report 可以准确定位实际运行产物。

---

# 17. Git Tag

推荐 Git Tag 与软件完整版本保持一致。

例如：

```text
v26Q4.18.AT-D.03
```

Build ID 一般不建议进入 Git Tag。

原因是：

Build 是 CI 产物属性，而 Git Tag 是源码状态属性。

因此：

Git Tag：

`v26Q4.18.AT-D.03`

Binary：

`26Q4.18.AT-D.03+B1842`

Commit：

`97940e0`

三者共同形成完整供应链身份。

---

# 18. GitHub Release

推荐 Release 名称：

```text
Cocktail Manager 26Q4.18.AT-D.03
```

Tag：

```text
v26Q4.18.AT-D.03
```

Asset：

```text
Cocktail.26Q4.18.AT-D.03+B1842.Windows.x86_64.zip
Cocktail.26Q4.18.AT-D.03+B1842.Linux.x86_64.tar.gz
Cocktail.26Q4.18.AT-D.03+B1842.Windows.x86_64.msi
Cocktail.26Q4.18.AT-D.03+B1842.Linux.x86_64.deb
Cocktail.26Q4.18.AT-D.03+B1842.Linux.x86_64.rpm
```

---

# 19. 维护版本

GA 后如果出现严重 Bug，可以进入维护发行。

可使用：

**HF — Hotfix**

例如：

`26Q4.24.HF.01+B2180`

Hotfix 不进入正常功能开发流程。

允许内容：

- 安全漏洞；
- 数据损坏问题；
- 启动失败；
- 严重兼容问题；
- 高优先级生产故障。

不得加入普通新功能。

---

# 20. 长期支持

未来如 Cocktail 建立长期维护版本，可使用：

**LTS — Long Term Support**

例如：

`27Q2.20.LTS.01+B3400`

LTS 重点维护：

- Security Fix；
- Critical Bug Fix；
- OS Compatibility；
- Java Compatibility；
- Minecraft Protocol Compatibility；
- 必要数据库迁移。

LTS 原则上不接收大型功能。

---

# 21. 设计原则总结

Cocktail 版本号不是单纯表示“数字大小”。

它同时表达：

**什么时候开发**

`26Q4`

**第几个核心迭代**

`.18`

**成熟到什么程度**

`.AT-D`

**进行了第几次发行修订**

`.03`

**具体是哪一次构建**

`+B1842`

因此：

`26Q4.18.AT-D.03+B1842`

本质上是一个完整的软件身份描述。

---

# 22. 最终规范

标准格式：

`YYQn.MAJOR.STAGE.MINOR+BUILD`

标准阶段：

`DT → DP → AT-D → AT → BT-D → BT → RC-D → RC → GA`

维护阶段：

`HF`

长期维护阶段：

`LTS`

典型版本：

`26Q4.18.AT-D.03+B1842`

本规范自采用之日起，建议所有 Cocktail 二进制、Release、安装包、Bug Report 和 CI 产物统一遵循该版本体系。
