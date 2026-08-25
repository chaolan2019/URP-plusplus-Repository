# SCU URP++ 主题与插件仓库

本仓库是 **SCU URP++ 主插件**的「主题 / 插件商店」内容源。主插件从这里拉取 `catalog.json` 获取可下载列表。

> **第三方开发规范见 [DEV_GUIDELINES.md](./DEV_GUIDELINES.md)**：投稿前必读（卡片样式 cardCss、暗色适配、按钮克制、CSS 覆盖与修 bug 纪律）。

## 目录结构

```
.
├── catalog.json        # 商店清单（核心，主插件从这里拉列表）
├── plugins/
│   └── urpppp.plugin.js   # 辅助插件（主插件装载式）
├── themes/
│   ├── flat.css / organic.css / brutal.css / neu.css   # 4 个官方主题
└── README.md           # 本说明 + 投稿规范
```

## catalog.json 条目字段

```jsonc
{
  "id": "flat",
  "type": "theme",              // theme | plugin
  "name": "极简扁平",
  "description": "…",
  "version": "1.0.0",           // 用于「检查更新」对比
  "author": "Chao_Lan",
  "repo": "https://github.com/…",  // 作者仓库（第三方自托管时指作者仓库）
  "minAPP": "1.9.4",            // 最低主插件版本
  "entry": [                    // 可下载产物（多源，主插件按序降级）
    "https://raw.githubusercontent.com/…/themes/flat.css",
    "https://cdn.jsdelivr.net/gh/…@main/themes/flat.css",
    "https://gh-proxy.com/https://raw.githubusercontent.com/…/themes/flat.css"
  ],
  "preview": ["#FFFFFF", "#000000", "#4A4A4A"],   // 主题预览色
  "cardCss": "…"                // 可选：主题卡片展示样式（作用于 .urppp-skin-card[data-skin="<id>"]）
}
```

## 第三方投稿：两种方式（都支持）

### 方式一 · PR 合并到本仓库（集中式，官方收录）
1. 把主题/插件产物放进本仓库对应目录（`themes/<id>.css` 或 `plugins/<id>.js`），并在 `catalog.json` 按上面字段加一条。
2. 提 **PR** 到本仓库，审核后合并。
3. 之后由你（或仓库维护者）统一发版更新 catalog。

### 方式二 · 自建仓库 + 添加到源（去中心化，社区共建）
1. 第三方**自己维护一个仓库**，放好产物 + **自己的 `catalog.json`**（条目 schema 同上，`entry` 指向自己的产物，`repo` 写自己的仓库）。
2. 主插件「插件商店」→「仓库源」里**添加该源 URL**，主插件合并拉取（官方源优先，同 id 冲突官方优先）。
3. 产物、版本、更新全部由作者自己管理，无需经过本仓库。

> 安全：自建源条目须带 `signature`（签名校验）；安装第三方内容前会提示「自担风险」。

## 投稿要求
- 主题只允许注入 CSS（`html[data-urppp-skin="<id>"]` 前缀），不执行任意 JS。
- 需要脚本能力的走 plugin 类型，并声明 `allowJS`。
- 版本号用语义化版本（用于「检查更新」对比）。
- 主题卡片样式建议在 catalog 项里带 `cardCss`（作用于 `.urppp-skin-card[data-skin="<id>"]`，用于商店展示）。
- **暗色适配**：若主题支持暗色模式（schema 项里有 `dark: true`），`cardCss` 必须同时提供暗色变体，用 `html.urppp-theme-dark` 前缀包裹：
  ```css
  .urppp-skin-card[data-skin="mytheme"]{ /* 亮色卡片 */ }
  html.urppp-theme-dark .urppp-skin-card[data-skin="mytheme"]{ /* 暗色卡片 */ }
  ```
  暗色变体需同步覆盖卡片底色、文字色、主按钮（`.urppp-skin-apply`）与次要按钮（`.urppp-set-btn.ghost`），否则暗色模式下会残留亮色样式。
