# 工具链

每个仓库只安装自己那一列。不要为了改文档去升级别人的 JDK。

| 仓库 | 必需 | 可选 |
|---|---|---|
| Cocktail | Rust stable（edition 2024）、Node.js 22+ | nfpm（Linux 打包脚本可自行下载）、WiX v3（Windows MSI）、Docker（容器实例） |
| ESPlus | JDK 21 | Qt6（Windows 密码窗，`native/build-pwprompt.bat`） |
| leaf | CMake ≥ 3.28、Ninja、GCC 13+ 或 Clang 17+、JDK 25 | Kotlin/Native（SDK 仍是脚手架） |
| docs | Git、能渲染 Markdown 的编辑器 | — |

JDK 不能混用：

- ESPlus 的 NeoForge 1.21.1 构建用 JDK 21。
- leaf 的 FFM 桥和 live harness 用 JDK 25。ForgeGradle live 恢复时需要 JDK 21，那是 Forge 那条线的限制，不把 leaf 主构建降回 21。
- Cocktail 控制面本身不要求 JDK；它启动的是实例里的 Minecraft JVM。

## 第一次启动

Cocktail：

```bash
cargo run -p cocktail-control
cd admin && npm install && npm run dev
```

ESPlus（Windows 脚本；Linux 用 `./gradlew build`）：

```bat
.\gradlew.bat build
```

leaf：

```bash
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Debug -DLEAF_BUILD_EXAMPLES=ON
cmake --build build
```

## 依赖锁定

- Cocktail：提交 `Cargo.lock`。改依赖时把 lock 一起提交。
- ESPlus：使用仓库里的 Gradle Wrapper，不要换成本机任意 Gradle。
- leaf：桥接使用各 `bridge/*/gradlew`。C++ 依赖保持在 CMake 里显式声明。

## 代理

Cocktail 在中文网络下会读 `COCKTAIL_PROXY`、`HTTPS_PROXY`，Windows 上还可读系统代理。插件里的 `cocktail_control` 在控制面进程内应答，不走这层代理。给插件加出站 HTTP 时使用 SDK 的 `cocktail_http`，并在 PR 里说明目标主机。
