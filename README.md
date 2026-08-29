# SCU URP++ 主题与插件仓库

本仓库是 **SCU URP++ 主插件**的「主题 / 插件商店」内容源。主插件从这里拉取 `catalog.json` 获取可下载列表。

> **投稿 / 开发前请先读 [DEV_GUIDELINES.md](./DEV_GUIDELINES.md)**：完整的 catalog 字段、主题/插件投稿、卡片样式、暗色适配、CSS 开发规范与常见坑。
>
> **主题投稿示例见 [THEME_EXAMPLE.md](./THEME_EXAMPLE.md)**（以「自然有机」为例）。
>
> **插件投稿示例见 [PLUGIN_EXAMPLE.md](./PLUGIN_EXAMPLE.md)**（以「辅助插件」为例）。

## 目录结构

```
.
├── catalog.json        # 商店清单（核心，主插件从这里拉列表）
├── plugins/
│   └── urpppp.plugin.js   # 辅助插件（官方）
├── themes/
│   └── flat.css / organic.css / brutal.css / neu.css   # 4 个官方主题
├── DEV_GUIDELINES.md   # 开发指南（投稿/开发必读）
└── README.md
```

## catalog.json

```jsonc
{
  "id": "flat",              // 唯一标识
  "type": "theme",           // theme | plugin
  "name": "极简扁平",
  "description": "…",
  "version": "1.0.0",        // 语义化版本，用于检查更新
  "author": "Chao_Lan",
  "repo": "https://github.com/…",   // 作者仓库
  "minAPP": "1.9.4",         // 最低主插件版本
  "entry": [                 // 可下载产物（多 URL，主插件按序降级）
    "https://raw.githubusercontent.com/…/themes/flat.css",
    "https://cdn.jsdelivr.net/gh/…@main/themes/flat.css",
    "https://gh-proxy.com/…"
  ],
  "preview": ["#FFFFFF", "#000000", "#4A4A4A"],
  "cardCss": "…"             // 第三方主题必填；官方主题由主插件内置样式
}
```

完整字段说明见 [开发指南](./DEV_GUIDELINES.md#二catalogjson-完整字段)。

## 投稿方式

- **方式一（集中式）**：把产物放进 `themes/<id>.css` / `plugins/<id>.js`，在 `catalog.json` 加一条，提 **PR** 合并。
- **方式二（去中心化）**：自建仓库放产物 + 自己的 `catalog.json`，在主插件「仓库源」添加该源 URL（条目需带 `signature`）。

详细流程见 [开发指南——双轨投稿](./DEV_GUIDELINES.md#八双轨投稿方式)。
> **快捷工具**：[contribute/](./contribute/) 目录提供主题骨架模板、catalog 条目模板与投稿自检脚本（
ode contribute/check.mjs 你的-catalog.json 你的主题.css），提 PR 前跑一遍全绿更快合并。
