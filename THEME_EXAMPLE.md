# 主题投稿示例：以「自然有机（organic）」为例

本示例基于官方主题「自然有机」，演示一个第三方主题投稿应具备的完整要素（产物、catalog 条目、卡片样式、能力声明）。字段与规范详见 [DEV_GUIDELINES.md](./DEV_GUIDELINES.md)。

---

## 1. 主题能力声明

| 能力 | 值 | 说明 |
|---|---|---|
| `dark` | `true` | 支持暗色模式，需提供暗色变体，主插件启用暗色开关 |
| `dynamic` | `false` | 不支持动态配色（种子色），主插件屏蔽种子色选项 |
| `palettes` | `false` | 不支持固定调色板，主插件屏蔽调色板项 |

能力声明必须与主题实际实现一致：`dynamic: false` 即主插件不会显示种子色内容，主题无需提供动态配色样式。

---

## 2. catalog 条目

```jsonc
{
  "id": "organic",
  "type": "theme",
  "name": "自然有机",
  "description": "奶油底与大地色，温暖圆角。不支持动态配色。",
  "version": "1.0.0",
  "author": "Chao_Lan",
  "repo": "https://github.com/chaolan2019/SCU-URP-plusplus",
  "minAPP": "1.9.4",
  "entry": [
    "https://raw.githubusercontent.com/chaolan2019/URP-plusplus-Repository/main/themes/organic.css",
    "https://cdn.jsdelivr.net/gh/chaolan2019/URP-plusplus-Repository@main/themes/organic.css",
    "https://gh-proxy.com/https://raw.githubusercontent.com/chaolan2019/URP-plusplus-Repository/main/themes/organic.css"
  ],
  "preview": ["#FAF6F1", "#5C4033", "#8B9D77"],
  "dark": true,
  "dynamic": false,
  "palettes": false
}
```

> 官方主题（flat / organic / brutal / neu）的卡片样式由主插件 `settings.css` 内置，因此官方主题的 catalog 条目**不包含** `cardCss`。以下 3、4 节展示的是「若作为第三方投稿」应如何编写产物与卡片样式。

---

## 3. 主题产物（organic.css）

### 3.1 变量声明（亮色）

所有规则必须以 `html[data-urppp-skin="organic"]` 为前缀，限定作用域：

```css
html[data-urppp-skin="organic"]{
  --radius:22px!important;
  --bg:#FAF6F1!important;
  --surface:#FFFCF7!important;
  --input-bg:#F3EDE4!important;
  --text:#3F2E24!important;
  --text-secondary:#6B5346!important;
  --border:#E7E0D6!important;
  --primary:#5C4033!important;
  --primary-hover:#4A3329!important;
  --ring:rgba(92,64,51,.16)!important;
}
```

### 3.2 暗色变体（支持暗色时必须提供）

`dark: true` 时须提供 `html[data-urppp-skin="<id>"].urppp-theme-dark` 变体，覆盖色板变量：

```css
html[data-urppp-skin="organic"].urppp-theme-dark,
html.urppp-theme-dark[data-urppp-skin="organic"]{
  --bg:#1C1712!important;
  --surface:#2A221B!important;
  --text:#F5EDE4!important;
  --text-secondary:#D2C0B0!important;
  --border:#4A3B30!important;
  --primary:#C4A484!important;
  --primary-hover:#D4B896!important;
  --ring:rgba(196,164,132,.22)!important;
}
```

### 3.3 组件规则

对页面元素应用主题化样式，统一使用前缀限定：

```css
html[data-urppp-skin="organic"] #urppp-clean-root .uc-card,
html[data-urppp-skin="organic"] #urppp-clean-root .uc-modal{
  border-radius:var(--radius)!important;
  border:1px solid var(--border)!important;
  box-shadow:var(--shadow)!important;
  background:var(--surface)!important;
}
html[data-urppp-skin="organic"] .btn-primary:not(.btn-app){
  background:var(--primary)!important;
  border-color:var(--primary)!important;
  color:#fff!important;
}
```

---

## 4. 卡片样式（cardCss，第三方必填）

第三方主题必须在 catalog 的 `cardCss` 字段提供商店卡片展示样式。作用域为 `.urppp-skin-card[data-skin="<id>"]`，**仅定义卡片底色与主按钮（`.urppp-skin-apply`）**：

```css
.urppp-skin-card[data-skin="organic"]{
  background:#FAF6F1; color:#5C4033;
  border-radius:16px; border:1px solid #E8DFD2;
  padding-bottom:52px;
}
.urppp-skin-card[data-skin="organic"] .urppp-skin-apply{
  background:#5C4033; color:#fff;
  border-radius:999px; border:none;
}
.urppp-skin-card[data-skin="organic"] .urppp-skin-apply.is-current{ background:#4A3329; }

html.urppp-theme-dark .urppp-skin-card[data-skin="organic"]{
  background:#2A221B; color:#F5EDE4; border-color:#4A3B30;
}
html.urppp-theme-dark .urppp-skin-card[data-skin="organic"] .urppp-skin-apply{
  background:#C4A484; color:#1C1712;
}
```

要点：
- 不在此定义次要按钮（`.urppp-set-btn.ghost`）。「仓库 / 删除」按钮由主插件以 `urppp-skin-apply` 样式统一渲染。
- 支持暗色时，须提供 `html.urppp-theme-dark` 变体并覆盖卡片底色、文字色与主按钮。

---

## 5. 投稿 Checklist

- [ ] 产物所有规则以 `html[data-urppp-skin="<id>"]` 为前缀。
- [ ] 支持暗色（`dark: true`）时提供 `urppp-theme-dark` 暗色变体。
- [ ] catalog 条目包含 `type` / `name` / `description` / `version` / `author` / `repo` / `entry`。
- [ ] `entry` 至少 2 个 URL 以保证降级可用。
- [ ] 第三方主题在 `cardCss` 提供卡片底色与主按钮样式（不含次要按钮）。
- [ ] 能力声明（`dark` / `dynamic` / `palettes`）与实际实现一致。
- [ ] 版本号为语义化版本，且与产物自带版本一致。
- [ ] 官方主题不写 `cardCss`（由主插件内置样式接管）。
