# SCU URP++ 第三方开发指南（DEV_GUIDELINES）

本仓库是 **SCU URP++ 主插件**的「主题 / 插件商店」内容源。本指南适用于：向本仓库投稿主题/插件、自建仓库作为商店源、以及所有修改主插件商店相关代码的开发者。**投稿、修 bug、提交更新前请通读一遍。**

---

## 目录

1. [商店整体结构](#一商店整体结构)
2. [catalog.json 完整字段](#二catalogjson-完整字段)
3. [主题（theme）开发规范](#三主题theme开发规范)
4. [插件（plugin）开发规范](#四插件plugin开发规范)
5. [商店 UI 与按钮规范](#五商店-ui-与按钮规范)
6. [CSS 开发规范与常见坑](#六css-开发规范与常见坑)
7. [主题状态与 UI 时序](#七主题状态与-ui-时序)
8. [双轨投稿方式](#八双轨投稿方式)
9. [版本管理](#九版本管理)
10. [修 bug 纪律](#十修-bug-纪律)

---

## 一、商店整体结构

```
URP++-Repository/
├── catalog.json          # 商店清单（主插件从这里拉取）
├── plugins/
│   └── urpppp.plugin.js  # 辅助插件（官方，主插件装载式）
├── themes/
│   ├── flat.css          # 4 个官方主题
│   ├── organic.css
│   ├── brutal.css
│   └── neu.css
├── README.md             # 仓库说明（精简）
└── DEV_GUIDELINES.md     # 本开发指南
```

### 主插件如何消费
- 主插件启动时从 `CATALOG_SOURCES`（catalog 的多个 URL 源）拉取 `catalog.json`，**按序降级**（raw → jsdelivr → gh-proxy），每个源带 **5 秒超时**，失败则整体降级返回空列表（商店不会卡「加载中」）。
- 主插件读取 `type` 字段区分 `theme` / `plugin`，用 `entry` 的多 URL 列表下载产物（按序降级）。
- 主题产物是 **CSS**（注入 `<style id="urppp-store-theme-<id>">`），刷新后从 `GM_getValue` 重新注入，不丢失。
- 插件产物是 **JS**（主插件装载式，通过 pluginManager 注册）。

---

## 二、catalog.json 完整字段

```jsonc
{
  // ===== 通用条目 =====
  "id": "flat",                 // 必填。唯一标识（字母/数字/下划线），主题须与产物 data-urppp-skin="<id>" 一致
  "type": "theme",              // 必填。theme | plugin
  "name": "极简扁平",            // 必填。展示名
  "description": "无阴影、硬边与纯色层次。",  // 必填。一句描述（商店卡片用）
  "version": "1.0.0",           // 必填。语义化版本，用于「检查更新」对比
  "author": "Chao_Lan",         // 必填。作者名（商店卡片显示）
  "repo": "https://github.com/…", // 必填。作者仓库（商店「仓库」按钮跳转；自建源指作者仓库）
  "minAPP": "1.9.4",            // 选填。最低主插件版本，低于则不展示
  "entry": [                    // 必填。可下载产物（多 URL，主插件按序降级）
    "https://raw.githubusercontent.com/…/themes/flat.css",
    "https://cdn.jsdelivr.net/gh/…@main/themes/flat.css",
    "https://gh-proxy.com/https://raw.githubusercontent.com/…/themes/flat.css"
  ],
  "preview": ["#FFFFFF", "#000000"],  // 主题选填。预览色（3 个色值），商店可用

  // ===== theme 专属 =====
  "dark": true,                // 能力声明：是否支持暗色模式。支持→需做暗色变体，插件显示暗色开关；不支持→插件屏蔽暗色按钮，用户不可用
  "dynamic": true,             // 能力声明：是否支持动态配色（种子色）。支持→插件显示种子色；不支持→屏蔽种子色内容
  "palettes": true,            // 能力声明：是否支持固定调色板。支持才显示调色板项
  "cardCss": "…",               // 第三方主题【必填】。卡片展示样式（见 三.3）。官方 4 主题由主插件内置样式，不需要 cardCss
  "downloads": 1234,           // 选填。下载量（有则显示；无则不显示，计数后端另配）

  // ===== plugin 专属 =====
  "allowJS": true,              // 插件必填。声明需要脚本能力
  "signature": "…",             // 自建源条目推荐。签名校验（防篡改）
}
```

> **注意**：`type` 为 `theme` 时，`id` 必须是主题 CSS 里的 `data-urppp-skin="<id>"` 值；`type` 为 `plugin` 时，`id` 必须与插件注册名一致。

---

## 三、主题（theme）开发规范

### 3.1 产物 CSS 基本要求
- **只注入 CSS，不执行任意 JS**。需要脚本能力走 plugin。
- 所有规则必须以 `html[data-urppp-skin="<id>"]` 为前缀（作用域限定在当前皮肤，避免泄漏到其它主题）。
- 覆盖策略：用 `!important` + 高 specificity 即可，不要用 `:not()` 做宽泛排除（会把商店预览按钮也误伤）。
- **暗色**：若主题支持暗色，需提供暗色变体 `html[data-urppp-skin="<id>"].urppp-theme-dark, html.urppp-theme-dark[data-urppp-skin="<id>"]`，覆盖色板变量、按钮、卡片。

### 3.2 catalog 条目
- 在 `catalog.json` 的 `items` 数组里加一条，`type: theme`，字段见[第二节](#二catalogjson-完整字段)。
- `entry` 至少 2 个 URL（raw + CDN + proxy），保证降级可用。
- `repo` 指向你的主题仓库（若自建源）方向。

### 3.3 卡片样式（cardCss）——第三方必填
商店的主/下载卡片用 `.urppp-skin-card` 展示每个主题。**第三方主题必须在 catalog 的 `cardCss` 里提供卡片展示样式**（官方 4 主题的卡片样式由主插件 `settings.css` 的 per-skin 内置，不需要 cardCss）。

`cardCss` 作用范围：`.urppp-skin-card[data-skin="<id>"]`。**只提供卡片底 + 主按钮 `.urppp-skin-apply`**，示例：

```css
.urppp-skin-card[data-skin="mytheme"] { background:#fff; color:#222; border-radius:14px; padding-bottom:52px; }
.urppp-skin-card[data-skin="mytheme"] .urppp-skin-apply { background:#2563eb; color:#fff; border-radius:999px; }
.urppp-skin-card[data-skin="mytheme"] .urppp-skin-apply.is-current { background:#1d4ed8; }
```

> **不要**在 cardCss 里写次要按钮（`.urppp-skin-card[data-skin=…] .urppp-set-btn.ghost`）。主插件的「仓库/删除」按钮已**复用 `urppp-skin-apply` 样式**（见[五]），你写了反而被主插件覆盖或造成冲突。

### 3.4 能力适配原则（重要，不是「所有主题都做暗色」）

主题通过能力字段声明自己支持什么（`dark` / `dynamic` / `palettes`），插件据此**提供选项或屏蔽选项**：

- **支持暗色（`dark: true`）**→ 主题提供暗色变体，插件**显示暗色模式开关**；`dark: false`（不适配）→ 插件**屏蔽暗色按钮**，用户不可用，主题无需做暗色。
- **支持动态配色（`dynamic: true`）**→ 插件显示种子色（主题色）选项；`dynamic: false` → **屏蔽种子色内容**（不显示、不划线）。
- **支持固定调色板（`palettes: true`）**→ 插件显示调色板项；不支持则屏蔽。

**所以**：只有 `dark: true` 的主题才需要 `html.urppp-theme-dark` 暗色变体；`dark: false` 的不用做，插件会自动把暗色开关隐藏。同理 `dynamic: false` 的主题不用提供种子色样式，插件自动屏蔽。

> 若主题声明了 `dark: true` 却不提供暗色变体，或声明 `dynamic: true` 却不配合，属于**能力与实现不符**，评审不通过。能力声明必须与实际产物一致。

### 3.5 悬停态
hover 时必须**背景与文字同步**：卡片 hover 的背景变化要**明显**（不要只改文字），且 `.urppp-skin-name` / `.urppp-skin-desc` 等子元素**跟着变色**。避免「背景变了文字没跟」或「只改文字背景不变」导致看不清。

---

## 四、插件（plugin）开发规范

- `type: plugin`，catalog 里 `allowJS: true`（必须声明）。
- 产物是 JS，通过**主插件装载式**注册（pluginManager 提供 `api.install/unregister/list/get/isEnabled`）。
- 插件注册时带的 `name`、`description`、`author`、`version`、`repo` 必须与 catalog 条目一致，否则商店管理页显示不对。
- `entry` 多 URL 降级，`repo` 指向插件仓库。
- 更新：改产物 + bump catalog 里的 `version` + bump 插件自带版本，三处一致。

---

## 五、商店 UI 与按钮规范

### 5.1 主题卡片结构
```html
<div class="urppp-skin-card" data-skin="<id>">
  <div class="urppp-skin-name">主题名</div>
  <div class="urppp-skin-meta">作者 · v版本 [· 下载量]</div>
  <p class="urppp-skin-desc">描述</p>
  <button class="urppp-skin-apply" data-theme-use="<id>">使用/使用中</button>   <!-- 右下 -->
  <button class="urppp-skin-apply urppp-store-del" data-theme-del="<id>">删除</button>  <!-- 应用钮左侧 -->
  <button class="urppp-skin-apply urppp-store-repo" data-repo="…">仓库</button>         <!-- 左下 -->
</div>
```

### 5.2 按钮一律复用 apply 样式（重要）
**「使用/下载」「删除」「仓库」三个按钮全部用 `urppp-skin-apply` 类**（删除/仓库加定位类 `urppp-store-del`/`urppp-store-repo`）。这样：
1. 每个按钮**跟该主题的应用按钮样式完全一致**（editorial 无框文字、neu 拟物凸起、brutal 粗框硬阴影+悬停动画、flat 黑框、apple 蓝胶囊、organic 棕胶囊）。
2. **自动免疫主题 CSS 污染**——主题 css（如 brutal.css）里有 `#urppp-settings-panel button:not(.urppp-nav-dot):not(.urppp-skin-apply):not(.urppp-set-swatch)` 这类宽泛规则，会把商店所有按钮刷成当前主题样式；**因为按钮含 `urppp-skin-apply`，会被 `:not(...)` 排除**，从而不被污染。

**不要**给仓库/删除单独写 `urppp-set-btn ghost` 样式，也不要自己造一套「克制的次要按钮」——那既跟主按钮不一致，又会被主题 CSS 污染（对，这就是之前几轮反复出问题的根源）。

### 5.3 定位
- apply 默认 `position:absolute; right:12px; bottom:12px`（右下 = 使用/下载）。
- `.urppp-store-repo { right:auto; left:12px; }`（左下 = 仓库）。
- `.urppp-store-del { right:78px; bottom:12px; }`（使用钮左侧 = 删除）。

### 5.4 卡片样式接管
- 第三方主题卡片样式走 catalog `cardCss`（见[三.3]）。
- 官方 4 主题的卡片（亮/暗 + 主按钮）由主插件 `settings.css` 的 per-skin 内置（`.urppp-skin-card[data-skin="flat"]` 等），不需要 cardCss。**catalog 不要给官方 4 主题写 cardCss**（会覆盖主插件的暗色变体，导致 brutal 暗色白卡）。

---

## 六、CSS 开发规范与常见坑

### 6.1 specificity 与 !important
- 同一份 `!important`，按 **id 数 → class/attr/pseudo 数 → element 数** 比较，高的胜；相同则按**声明顺序**，靠后的胜。
- 全局元素带 `!important`（如 `.urppp-set-btn.ghost { background: var(--input-bg) !important }`）会压掉普通卡片样式。**想让某个卡片样式赢，必须用更高 specificity + 同样 `!important` + 放在旧规则后面**。

### 6.2 常见坑（都是踩过的）
| 坑 | 现象 | 修法 |
|---|---|---|
| 主题 css 用 `button:not(...)` 宽泛覆盖 | 应用某主题后商店所有按钮被刷成该主题样式 | 按钮复用 `urppp-skin-apply`（被 `:not(.urppp-skin-apply)` 排除） |
| 暗色 ghost 规则漏 `background` | 按钮背景继承全局 `--input-bg`（当前主题），变黑块 | 暗色按钮补 `background: transparent` 或改用 apply 样式 |
| catalog `cardCss`（亮色）注入晚 | 覆盖主插件暗色变体 → brutal 暗色白卡 | 官方主题不要在 catalog 写 cardCss |
| `fetch` 无超时 | 网络挂起时商店永远「加载中」 | catalog 拉取用 `Promise.allSettled` + 5 秒超时 |
| 应用主题后不重渲染 | 「使用中」徽标不更新；删除正在用的主题残留缓存 | 应用/删除后立即重渲染列表；删除正在用的回落到默认 apple |

### 6.3 不要重复定义
同一选择器（如某主题暗色卡）只写一份。**被后面规则覆盖的死代码要删掉**，否则体积只增不减。

---

## 七、主题状态与 UI 时序

- 打开主题商店 → 拉 catalog → 渲染下载 tab（排除已下载）+ 管理 tab（内置 + 已下载）。
- **下载主题**：写入 `GM_setValue('urppp_theme_css_<id>', css)` + 注入 `<style id="urppp-store-theme-<id>">` → **立即刷新**管理/下载列表。
- **应用主题**：`setSkin(id)` → `applySkinAttr` + `applyTheme` → **同步就地更新**所有卡片的 `is-active` 与「使用中/使用」文本（不要 await 拉 catalog，否则延迟）。刷新后从 `GM_getValue` 重新注入已下载主题 CSS，不丢失。
- **删除主题**：清 `GM_setValue` + `removeStoreThemeStyle`（从 DOM 删 `<style>`）+ 若删的是当前应用主题则**回落默认 apple**（`applySkinAttr` + `applyTheme`），不残留缓存。
- 刷新页面时：`init()` 先 `injectAllStoreThemeStyles()`（注入所有已下载主题 CSS + 设 `data-urppp-skin`），再 `applyTheme`——避免先白后主题色（白闪）。

---

## 八、双轨投稿方式

### 方式一 · PR 合并到本仓库（集中式，官方收录）
1. 放产物到 `themes/<id>.css` 或 `plugins/<id>.js`，在 `catalog.json` 按[第二节](#二catalogjson-完整字段)加条目。
2. 提 **PR**，审核合并。
3. 由你/维护者统一发版更新 catalog。

### 方式二 · 自建仓库 + 添加到源（去中心化）
1. 第三方**自己维护仓库**，放产物 + **自己的 `catalog.json`**（schema 同上，`entry`/`repo` 指向自己的仓库）。
2. 主插件「插件商店」→「仓库源」添加该源 URL，主插件合并拉取（官方源优先，同 id 冲突官方优先）。
3. 产物、版本、更新由作者自己管理，无需经过本仓库。
4. **安全**：自建源条目须带 `signature`（签名校验），安装第三方内容前会提示「自担风险」。

---

## 九、版本管理

- 版本号用语义化 `x.y.z`，用于「检查更新」对比。
- **每次改动必须 bump**（主插件版本 + catalog `version` + 产物自带版本，三处一致），否则用户检查更新不生效。
- 改动在 `CHANGELOG.md` 记录（用户可感知的功能/修复）。

---

## 十、修 bug 纪律

1. **修一次，回望一次**：修完回头扫一遍改动，删掉冗余/重复/死代码，**让体积只减不增**。
2. 动手前先找**根因**（是不是历史遗留的重复规则覆盖 / 主题 CSS 污染），**不要靠反复打补丁**——补丁越多越乱，体积越大。
3. 改 CSS 前先确认**伪类 / specificity / 注入顺序**，一次写对。
4. 所有改动**先在本地 git 提交推进**，等用户验证通过后再推远程（主插件代码未验证不推）。
