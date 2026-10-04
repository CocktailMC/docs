# 项目地图

四个公开仓库分工不同，许可也不相同。改代码前先确认仓库和许可。

```text
CocktailMC
├── Cocktail     控制面：管进程、Docker、备份、面板
│     └── WASM 插件（Extism）可安装并代理 ES+
├── ESPlus       进游戏进程的安全中间件（NeoForge 1.21.1）
├── leaf         原生模组运行时，不依赖控制面
└── docs         组织流程与跨仓库说明
```

| 关系 | 说明 |
|---|---|
| Cocktail → ESPlus | `crates/plugins/esplus` 把 ES+ jar 装进实例，并代理其面板。ES+ 本体仍在 `CocktailMC/ESPlus`。 |
| Cocktail 与 leaf | 同组织、不同产品。leaf 模组不经过 Cocktail 控制面加载。 |
| docs | 不放实现代码。架构长文留在源码仓库。 |

## 许可边界

- Cocktail、leaf：Apache-2.0。
- ESPlus：LGPL-3.0-or-later。把它链进别的作品时，按 LGPL 处理衍生与分发，不要默认成 Apache-2.0。
- 本仓库目前没有 `LICENSE` 文件。引用这里的流程文字可以，把整仓再许可成其他协议之前需要先补许可。

## 各项目入口

- [Cocktail](cocktail.md)
- [ESPlus](esplus.md)
- [leaf](leaf.md)
