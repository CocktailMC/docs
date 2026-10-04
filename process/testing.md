# 测试

合并前在本地跑与改动相交的命令。仓库尚无 GitHub Actions，绿色以 PR 描述里的命令输出为准。

## Cocktail

| 改动 | 命令 |
|---|---|
| Rust 控制面或插件 | `cargo test --workspace` |
| 风格 | `cargo fmt --check` 与 `cargo clippy --workspace --all-targets -- -D warnings` |
| 管理端 | `cd admin && npm run build`（有 lint 脚本时先 `npm run lint`） |
| 只改 Markdown | 通读链接与命令，不必跑全量编译 |

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

[versioning.md](../versioning.md) 第 13 节要求 DT 发布前通过：Cargo test、Clippy、Rustfmt、前端 lint、前端构建、Windows 构建、Linux 构建、打包。这些检查在 Actions 落地前由发版人在两台系统上执行并记在 Release 正文。任一失败则不发 DT。
