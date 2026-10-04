# 测试

合并前在本地跑与改动相交的命令。Cocktail 的 PR 还会跑 [CI](https://github.com/CocktailMC/Cocktail/blob/main/.github/workflows/ci.yml)。ESPlus 和 leaf 仍以本地结果为准。

## Cocktail

| 改动 | 命令 |
|---|---|
| 控制面 | `cargo test -p cocktail-control` |
| 风格 | `cargo fmt --all -- --check`，以及 `cargo clippy -p cocktail-control --all-targets` |
| WASM 插件 | `cargo build --target wasm32-unknown-unknown -p cocktail-plugin-watchdog -p cocktail-plugin-speclint -p cocktail-plugin-gameops -p cocktail-plugin-esplus` |
| 管理端 | `cd admin && npm ci && npm run lint && npm run build` |
| 只改 Markdown | 通读链接与命令，不必跑全量编译 |

插件是 `cdylib`，用 `wasm32-unknown-unknown` 编译，不进宿主上的 `cargo test`。clippy 目前不把警告当成失败；`scripts/release/rules.toml` 里的 `clippy_deny_warnings` 打开后才会。

插件改动额外说明：调用的宿主函数、是否实现 `tick`、对已有 KV 键的兼容。

手工路径（控制面 + 管理端都起来之后）：

1. 首次引导创建管理员并重新登录。
2. 创建一个实例，启动、在控制台执行 `stop`，确认状态回到已停止。
3. 若改动热接管：杀掉控制面进程后重启，仍在跑的实例保持运行中。

## ESPlus

```bat
.\gradlew.bat build
```

改面板时先 `.\gradlew.bat -p panel bootJar`，再完整 `build`，确认面板 jar 打进 mod jar。

手工路径：

1. 默认 `panelPassword` 且 `panelAllowDefaultPassword = false` 时，面板不起。
2. 改成强密码后再启动，本机 `127.0.0.1:8088` 可登录。
3. `/setoppw` 后，未升权的 OP 不能执行受保护指令；升为 moderator 或 admin 后 `/sudo` 可以。
4. 审计里的密码参数是脱敏的。

## leaf

```bash
cmake --build build
ctest --test-dir build --output-on-failure
./scripts/smoke-three-loaders.sh
```

动到 live 桥或玩家 API 时，再跑：

```bash
./scripts/live-runserver-smoke.sh fabric
./scripts/live-runserver-smoke.sh neoforge
python3 tooling/leaf-cli/leaf.py doctor
python3 tooling/leaf-cli/leaf.py verify dist/leafmods/greeter.leafmod
```

Forge live 在当前 JDK 25 工作区不能作为合并门禁。改了 Forge 桥时注明「源码对齐，live resolve 未跑」。

## docs

- 相对链接指向存在的文件。
- 命令与对应仓库 README 一致。
- 版本示例符合 [versioning.md](../versioning.md)，或明确标成「当前 Cargo 过渡字符串」。

## 和 DT 流水线的对应

推到 Cocktail 的 `main` 时，[DT release](https://github.com/CocktailMC/Cocktail/blob/main/.github/workflows/dt-release.yml) 先跑上面的检查。检查失败就不规划版本。通过之后，只有有效 PR 达到 2 个才打包 Linux 和 Windows 并发布。规则见 [automation.md](automation.md)。
