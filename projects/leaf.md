# leaf（LEAFMC）

仓库：<https://github.com/CocktailMC/leaf>  
默认分支：`main`  
许可：Apache-2.0  
语言：C++23 引擎，Kotlin/Native SDK 脚手架，Java 桥接

LEAFMC 是跨加载器、跨版本的 Minecraft 原生模组运行时。Forge、Fabric、NeoForge 只做薄桥。Leaf Mod 只依赖 Leaf API / ABI，不依赖 `net.minecraft.*` 或加载器包。

架构长文在源码仓库 `docs/architecture/`，本页只保留组织视角。模块进度以该目录的 `STATUS.md` 为准。

## 硬规则

1. Leaf Mod 不依赖 Minecraft 或加载器的 Java 包。
2. 插件边界是纯 C ABI：不跨边界传递 STL、异常、RTTI。
3. 游戏对象以带类型的 handle 暴露，不是 Java 引用。
4. 版本和加载器差异用 capability 表达，不写 `if (fabric)`。
5. 桥只启动引擎，不负责加载 `.leafmod`。

## 目录

```text
leaf/
├── abi/           稳定 C ABI 头
├── engine/        C++23 运行时
├── sdk/           语言 SDK
├── bridge/        Fabric / Forge / NeoForge 引导
├── minecraft/     分版本 ABI
├── tooling/       leaf-cli、打包、安装
├── examples/      示例 leafmod
├── tests/         引擎测试
└── docs/architecture/
```

架构文档按模块编号：

| 文件 | 模块 |
|---|---|
| `module-00-core.md` | 错误码、handle、C ABI 基底 |
| `module-01-loader.md` | 发现与依赖解析 |
| `module-02-native-load.md` | 原生包加载与生命周期 |
| `module-03-events.md` | 事件总线 |
| `module-04-scheduler.md` | 调度 |
| `module-05-bridge.md` | 加载器桥 |
| `module-06-minecraft-abi.md` | Minecraft ABI |
| `module-07-sdk.md` | C++23 与 Kotlin/Native SDK |
| `module-08-three-loader-runtime.md` | 三加载器运行与安装布局 |
| `module-error-codes.md` | 错误码 |
| `STATUS.md` | 已验证进度 |

## 构建

需要 CMake ≥ 3.28、C++23 编译器（GCC 13+ / Clang 17+ / 近期 MSVC）、建议 Ninja，以及 JDK 25（FFM 桥与 harness；`LEAF_JDK` 或 `~/.local/jdk25`）。

```bash
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Debug -DLEAF_BUILD_EXAMPLES=ON
cmake --build build
ctest --test-dir build --output-on-failure
./scripts/smoke-three-loaders.sh
./scripts/assemble-install.sh
```

桥接 JAR：`cd bridge && ./gradlew jar`（Gradle 9 + JDK 25）。

已在本机验证的路径包括离线三加载器冒烟，以及 Fabric、NeoForge 的 live `runServer`。Forge live 源码对齐，ForgeGradle 解析仍待 JDK 21 环境；当前工作区常用 JDK 25，live 验证优先 NeoForge。

## 版本

leaf 还没有独立的发版通道。开始发布二进制时，Git tag 与文件名遵循 [versioning.md](../versioning.md)，并在 Release 说明里写上 `LeafApi` 版本（进度快照里记录为 `LeafApiV1` 的递增小版本）。ABI 槽位变化必须同时更新示例包，避免旧 `.leafmod` 在新宿主上错位。
