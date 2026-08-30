# SCU URP++ 第三方开发指南

本仓库是 SCU URP++ 主插件的「主题 / 插件商店」内容源。本指南定义向本仓库投稿主题 / 插件、自建仓库作为商店源，以及修改主插件商店相关代码时应遵循的规范。投稿与开发前请通读。

---

## 目录

1. [商店整体结构](#一商店整体结构)
2. [catalog.json 字段规范](#二catalogjson-字段规范)
3. [主题开发规范](#三主题开发规范)
4. [插件开发规范](#四插件开发规范)
5. [商店 UI 与按钮规范](#五商店-ui-与按钮规范)
6. [CSS 开发规范](#六css-开发规范)
7. [状态与 UI 时序](#七状态与-ui-时序)
8. [投稿方式](#八投稿方式)
9. [版本管理](#九版本管理)

---

## 一、商店整体结构

```
URP++-Repository/
├── catalog.json          # 商店清单，主插件据此拉取
├── plugins/
│   └── urpppp.plugin.js  # 官方辅助插件
├── themes/
│   ├── flat.css          # 官方主题（4 套）
│   ├── organic.css
│   ├── brutal.css
│   └── neu.css
├── README.md             # 仓库说明
└── DEV_GUIDELINES.md     # 本指南
```

### 主插件的消费方式

- 主插件从 `CATALOG_SOURCES`（catalog 多源 URL）拉取 `catalog.json`，按配置的顺序降级。每个源设置 5 秒超时；全部失败则返回空列表，商店不阻塞加载。
- 主插件按 `type` 字段区分 `theme`（CSS）与 `plugin`（JS）。产物经 `entry` 中的多 URL 降级下载。
- 主题产物为 CSS，写入 `GM_setValue` 并在刷新后重新注入，保证持久生效。
- 插件产物为 JS，经主插件装载式注册（`pluginManager`）。

---

## 二、catalog.json 字段规范

```jsonc
{
  // 通用字段
  "id": "flat",                 // 唯一标识。theme 须与 data-urppp-skin="<id>" 一致；plugin 须与注册名一致
  "type": "theme",              // theme | plugin
  "name": "极简扁平",            // 展示名
  "description": "无阴影、硬边与纯色层次。",  // 卡片描述
  "version": "1.0.0",           // 语义化版本，用于检查更新
  "author": "Chao_Lan",         // 作者名
  "repo": "https://github.com/…", // 作者仓库，商店「仓库」按钮跳转
  "minAPP": "1.9.4",            // 最低主插件版本，低于则不展示
  "entry": [                    // 可下载产物，多 URL 降级
    "https://raw.githubusercontent.com/…/themes/flat.css",
    "https://cdn.jsdelivr.net/gh/…@main/themes/flat.css",
    "https://gh-proxy.com/…"
  ],
  "preview": ["#FFFFFF", "#000000"],  // theme 预览色（3 个色值）

  // theme 专属
  "dark": true,                 // 能力声明：是否支持暗色模式
  "dynamic": true,              // 能力声明：是否支持动态配色（种子色）
  "palettes": true,             // 能力声明：是否支持固定调色板
  "cardCss": "…",               // 主题（官方独立 + 第三方）必填；主插件不再内置独立主题卡片样式，一律由 catalog cardCss 提供（须含 hover 动效/暗色变体/次要按钮色）
  "downloads": 1234,            // 下载量（可选，无则不显示）

  // plugin 专属
  "allowJS": true,              // 必填，声明脚本能力
  "signature": "…"              // 自建源条目推荐，签名校验
}
```

**能力声明语义**：`dark` / `dynamic` / `palettes` 必须与主题实际实现一致。声明支持但未实现，或实现未声明，均属不符合规范。

---

## 三、主题开发规范

### 3.1 产物要求

- 主题仅注入 CSS，不执行任意 JavaScript。需要脚本能力者应投稿为 plugin。
- 所有规则必须限定于 `html[data-urppp-skin="<id>"]` 作用域，避免影响其他主题。
- 覆盖样式时使用 `!important` 与高 specificity；避免使用 `:not()` 做宽泛排除，以免误伤商店预览按钮。
- 支持暗色（`dark: true`）时，提供暗色变体 `html[data-urppp-skin="<id>"].urppp-theme-dark`。
- **控件覆盖完整性**：按 [THEME_EXAMPLE.md](./THEME_EXAMPLE.md#13b-主题可美化控件全清单对照检查避免漏项) 的 A~I 分组（页面骨架 / 卡片面板 / 按钮 / 表单 / 表格 / 状态提示 / 导航分页 / 清爽模式 / 其他）逐项对照，每个分组至少覆盖核心控件，避免测试时漏美化。
- **语义色覆盖**：应覆盖 `--success` / `--danger` / `--warning` / `--info` 四个语义色变量（亮暗两套），使状态色（成功/失败/警告/信息）跟随主题。若不覆盖则用主插件默认值。分类色板（课程类型、未评状态）除外。

### 3.2 catalog 条目

- 在 `items` 数组中添加条目，`type: theme`，字段见第二节。
- `entry` 至少提供 2 个 URL 以保证降级可用。
- `repo` 指向主题仓库（自建源场景）。

### 3.3 卡片样式（cardCss）

第三方主题必须在 catalog 的 `cardCss` 中提供卡片展示样式。作用域为 `.urppp-skin-card[data-skin="<id>"]`。

**卡样式三种来源（主插件按优先级取，均写 `GM urppp_card_css_<id>` 缓存）**：

1. **catalog 条目的 `cardCss` 字段**（投稿 / 自建源）——下载时缓存到本地，优先级最高；
2. **主题 CSS 文件内自带的 `.urppp-skin-card[data-skin="<id>"]` 规则段**（本地导入）——主插件导入时自动提取缓存，优先于自动生成；
3. **自动生成默认卡样式**（从主题变量 `--surface`/`--text`/`--border`/`--primary` 推导亮暗两套 + 主/次要按钮 + hover）——未提供 cardCss 时的兜底，保证卡不裸奔。

> 本地导入主题（设置 → 添加本地主题）**推荐在 CSS 文件末尾直接写卡样式段**（等效 catalog 的 cardCss），导入即生效且完全由你掌控；不写则走自动生成（样式跟随主题主色，但不保证你想要的细节）。

**cardCss 应覆盖的内容**：

1. 卡片底色（`background`）与文字色（`color`），以及 `border-radius` / `border` / `box-shadow`；需预留底部空间 `padding-bottom:52px` 给按钮。
2. 卡片标题与描述颜色（`.urppp-skin-name` / `.urppp-skin-desc`，如无则继承 `color`）。
3. 主按钮 `.urppp-skin-apply`（`background` / `color` / `border` / `border-radius`）及其 `is-current` / `hover` 状态。
4. 支持暗色（`dark: true`）时，提供 `html.urppp-theme-dark` 变体，并**同步覆盖**卡片底色、文字色与主按钮。

```css
.urppp-skin-card[data-skin="mytheme"] { background:#fff; color:#222; border-radius:14px; border:1px solid #eee; padding-bottom:52px; }
.urppp-skin-card[data-skin="mytheme"] .urppp-skin-name,
.urppp-skin-card[data-skin="mytheme"] .urppp-skin-desc { color:#555; }
.urppp-skin-card[data-skin="mytheme"] .urppp-skin-apply { background:#2563eb; color:#fff; border-radius:999px; }
.urppp-skin-card[data-skin="mytheme"] .urppp-skin-apply.is-current { background:#1d4ed8; }
.urppp-skin-card[data-skin="mytheme"] .urppp-skin-apply:hover { background:#3b82f6; }

html.urppp-theme-dark .urppp-skin-card[data-skin="mytheme"] { background:#1e1e1e; color:#eee; border-color:#333; }
html.urppp-theme-dark .urppp-skin-card[data-skin="mytheme"] .urppp-skin-name,
html.urppp-theme-dark .urppp-skin-card[data-skin="mytheme"] .urppp-skin-desc { color:#ccc; }
html.urppp-theme-dark .urppp-skin-card[data-skin="mytheme"] .urppp-skin-apply { background:#3b82f6; color:#fff; }
```

**限定**：cardCss 不应定义次要按钮（`urppp-set-btn.ghost`）。「仓库 / 删除」按钮由主插件统一以 `urppp-skin-apply` 样式渲染（见第五节），在 cardCss 中定义会导致覆盖冲突。

**官方与第三方**：

- **官方独立主题**（flat / organic / brutal / neu）的卡片样式**已从主插件 `settings.css` 脱离**，统一由 catalog 的 `cardCss` 提供。`cardCss` 必须**完整**：覆盖卡片底色/文字色、主按钮 `.urppp-skin-apply`、**次要按钮 `.urppp-store-repo` / `.urppp-store-del`（仓库/删除，须跟主按钮同主题色）**、hover 动效、`is-current`，以及 `html.urppp-theme-dark` 暗色变体。主插件打开设置/商店时按需注入 catalog cardCss（官方与第三方一致）。

> **cardCss 会在下载主题时被主插件缓存到本地**（`GM urppp_card_css_<id>`），皮肤卡 / 主题选择 / 商店优先使用本地缓存（不依赖线上拉取，避免先残缺后正常）。**因此 cardCss 必须完整**——若不完整，本地缓存后皮肤卡会一直残缺。

> **注意**：脱离后若 `cardCss` 未写全（缺次要按钮色 / hover 动效 / 暗色变体），商店卡片会明显退化（按钮默认色、无悬停动效、暗色残留）。**完整书写是硬性要求**，可对照官方 catalog 的 `cardCss` 或 THEME_EXAMPLE。

> 书写参考：可直接对照官方主题的完整 `cardCss`（catalog 中 flat / organic / brutal / neu 条目），示例见 [THEME_EXAMPLE.md](./THEME_EXAMPLE.md#14-卡片样式cardcss第三方必填)。

### 3.4 能力适配原则

主题通过能力字段声明支持范围。主插件依据能力提供或禁用对应选项：

- `dark: true` → 主题提供暗色变体，主插件启用暗色开关；`dark: false` → 主插件禁用暗色开关，主题无需适配暗色。
- `dynamic: true` → 主插件启用种子色选项；`dynamic: false` → 禁用种子色选项。
- `palettes: true` → 主插件启用固定调色板；否则禁用。

能力声明与实现不一致（如声明 `dark: true` 但未提供暗色变体）视为不符合规范，评审不通过。

### 3.5 悬停态

悬停时背景与文字须同步变化。背景变化应明显，且 `urppp-skin-name`、`urppp-skin-desc` 等子元素随之调整颜色，避免仅改变单个要素导致可读性下降。

---

## 四、插件开发规范

插件以 IIFE（自执行函数）形式发布，通过主插件注入的装载接口完成注册。完整参考见辅助插件（`plugins/urpppp.plugin.js`）。

### 4.1 注册接口

插件检测主插件注入的装载接口 `window.__urpppPlugin`：

```js
(() => {
  'use strict';
  const VERSION = '1.5.3';
  // …实现…

  const isPluginMode = typeof window.__urpppPlugin === 'object' && !!window.__urpppPlugin;
  if (isPluginMode) {
    try {
      window.__urpppPlugin.register({
        id: 'assist',           // 唯一标识，须与 catalog 的 id 一致
        type: 'plugin',
        name: '辅助插件',        // 展示名，须与 catalog 的 name 一致
        version: VERSION,       // 须与 catalog 的 version 一致
        subpanels: {            // 可选：插件子面板，挂到主插件设置面板
          login:   { label: '登录助手', open: () => openLoginPanel() },
          eval:    { label: '评教助手', open: () => openEvalPanel() },
          session: { label: '会话保持', open: () => openSessionPanel() },
        }
      });
    } catch (_) { /* ignore */ }
  }
})();
```

要点：
- **`id` / `name` / `version` 必须与 catalog 条目一致**，否则商店管理页显示异常、检查更新不生效。
- `subpanels`：每个子面板 `{ label, open }`，`open` 为打开对应设置面板的回调。
- `type` 固定为 `plugin`。

### 4.2 降级与独立运行

插件可能脱离主插件运行（独立 userscript）。当 `window.__urpppPlugin` 不存在时，应降级为独立模式：

```js
try {
  GM_registerMenuCommand('URP++辅助：打开设置说明', () => {
    alert('请启用 URP++ 主脚本，点击顶栏齿轮，在设置底部配置相关助手。');
  });
  GM_registerMenuCommand('URP++辅助：立即识别验证码', () => { resumeAutoLogin(); });
} catch (_) {}
```

### 4.3 向外部暴露 API（可选）

为便于主插件 / 控制台 / 扩展调用，可将核心能力挂到全局：

```js
window.__urppppAssist = {
  version: VERSION,
  runLogin: mainLogin,
  runEval: runEvaluationAssist,
  startFullAuto: startFullAutoEvaluation,
  stopFullAuto: stopFullAuto,
  injectSettings: injectSettingsPanel,
};
```

### 4.4 持久化与依赖

- 插件使用 `GM_getValue / GM_setValue` 持久化，存储键建议加命名空间前缀（如 `urpppp_assist_v1_...`）避免冲突。
- 可依赖主插件已注入的 `window.__urpppPlugin` 提供的环境；访问教务页 DOM / jQuery 时需判空并防多次绑定（`window.__xxxBound` 标记）。
- 设置面板项通过主插件设置面板注入（辅助插件用 `injectSettingsPanel`）。

### 4.5 catalog 与生命周期

- catalog 中 `type: plugin`，`allowJS: true`，`entry` 提供多 URL 降级，`repo` 指向插件仓库。
- 主机通过 `pluginManager`（`api.install / unregister / list / get / isEnabled`）管理插件装载与卸载，插件需在 `unregister` 时清理 DOM / 监听 / 定时器。
- 更新时同步提升插件自带 `version` 与 catalog `version`。

---

## 五、商店 UI 与按钮规范

### 5.1 主题卡片结构

```html
<div class="urppp-skin-card" data-skin="<id>">
  <div class="urppp-skin-name">主题名</div>
  <div class="urppp-skin-meta">作者 · v版本 [· 下载量]</div>
  <p class="urppp-skin-desc">描述</p>
  <button class="urppp-skin-apply" data-theme-use="<id>">使用/使用中</button>
  <button class="urppp-skin-apply urppp-store-del" data-theme-del="<id>">删除</button>
  <button class="urppp-skin-apply urppp-store-repo" data-repo="…">仓库</button>
</div>
```

### 5.2 按钮一致性

「使用 / 下载」「删除」「仓库」三个按钮统一使用 `urppp-skin-apply` 类（删除与仓库附加定位类 `urppp-store-del` / `urppp-store-repo`）。该约定保证：

- 按钮的视觉样式与对应主题的应用按钮完全一致。
- 主题 CSS 中 `button:not(.urppp-nav-dot):not(.urppp-skin-apply):not(.urppp-set-swatch)` 一类的宽泛规则会排除含 `urppp-skin-apply` 的按钮，从而避免按钮被当前主题样式污染。

### 5.3 定位

- `urppp-skin-apply`：`position:absolute; right:12px; bottom:12px`（右下，使用 / 下载）。
- `.urppp-store-repo`：`right:auto; left:12px`（左下，仓库）。
- `.urppp-store-del`：`right:78px; bottom:12px`（使用按钮左侧，删除）。

### 5.4 卡片样式归属

- 官方独立主题（flat / organic / brutal / neu）：卡片样式由 catalog `cardCss` 提供，主插件不再内置。
- 内置主题（apple / editorial）：卡片样式由主插件 `settings.css` per-skin 内置（不在 catalog）。
- 第三方主题：卡片样式由 catalog `cardCss` 提供，必须完整书写（含次要按钮色 / hover / 暗色变体）。

---

## 六、CSS 开发规范

### 6.1 specificity 与 !important

同一 `!important` 声明，按 id 数、class / attr / pseudo 数、element 数依次比较，高者优先；相同则按声明顺序，后者胜。全局元素若携带 `!important`（如 `.urppp-set-btn.ghost { background: var(--input-bg) !important }`），会覆盖普通卡片样式。需要卡片样式获得优先级时，应使用更高 specificity 的 `!important` 声明并置于旧规则之后。

### 6.2 常见问题与规避

| 问题 | 现象 | 规避 |
|---|---|---|
| 主题 CSS 以 `button:not(...)` 宽泛覆盖 | 应用主题后商店按钮被刷成该主题样式 | 按钮复用 `urppp-skin-apply`（被 `:not(.urppp-skin-apply)` 排除） |
| 暗色按钮规则缺失 `background` | 按钮背景继承全局 `--input-bg`（当前主题）而异常 | 暗色按钮补 `background: transparent` 或复用 apply 样式 |
| catalog `cardCss` 缺失次要按钮色/hover | 脱离后仓库/删除按钮显示默认色、悬停无动效 | `cardCss` 须覆盖 `.urppp-store-repo`/`.urppp-store-del` 主题色 + hover 动效 + 暗色变体 |
| 本地导入主题卡完全原生无样式 | 皮肤卡/主题管理无任何主题样式 | 主插件已兜底：CSS 内含 `.urppp-skin-card` 段则提取，否则从主题变量自动生成；推荐在 CSS 末尾写卡样式段 |
| 状态色写死 hex 不变量化 | 主题切换后状态色不跟随（亮暗不变），跨文件重复难改 | 状态色一律用 `var(--success)` / `var(--danger)` / `var(--warning)` / `var(--info)`，勿写死 `#15803d` / `#b91c1c` 等 |
| catalog 请求无超时 | 网络异常时商店停留在加载态 | 使用 `Promise.allSettled` + 5 秒超时 |
| 应用 / 删除主题后不刷新 | 「使用中」等状态不更新，删除正用主题残留缓存 | 应用 / 删除后立即刷新列表；删除正用主题回退默认主题 |

### 6.3 定义唯一性

同一选择器（如某主题的暗色卡片）仅定义一次。被后续规则覆盖的死代码应移除，避免体积膨胀。

### 6.4 语义色变量（新）

主插件内置 4 个语义色变量，所有「状态色」统一由它们驱动：

| 变量 | 亮色默认 | 暗色默认 | 语义 |
|---|---|---|---|
| `--success` | `#15803D` | `#4ADE80` | 成功 / 通过 / 已装 / 开启 |
| `--danger` | `#B53434` | `#F87171` | 失败 / 未过 / 错误 / 删除 |
| `--warning` | `#B45309` | `#FBBF24` | 警告 / 待办 / 未评 |
| `--info` | `#0A84FF` | `#60A5FA` | 信息 / 进行中 / 链接蓝 |

**规范**：
- 主插件 CSS 新增状态色时**必须用这 4 个变量**，禁止写死 hex（已写入 6.2 常见问题）。
- 第三方主题**应覆盖这 4 个变量**（亮暗两套），使状态色跟随主题气质；不覆盖则用默认值。
- 暗色下状态色自动变浅（主题变量已处理），无需逐条写暗色变体。
- **例外**：分类色板（如清爽模式课程类型 `uc-slot.kind-*`：course/exam/lab/borrow、成绩未评 `uneval` 蓝/橙）是功能区分色，**不用**语义变量，保持固定。

---

## 七、状态与 UI 时序

- 打开商店 → 拉取 catalog → 渲染下载列表（排除已下载）与管理列表（内置 + 已下载）。
- 下载主题：写入 `GM_setValue('urppp_theme_css_<id>', css)` 并注入 `<style id="urppp-store-theme-<id>">`，随后刷新下载与管理列表。
- 应用主题：`setSkin(id)` → `applySkinAttr` + `applyTheme`，同步更新各卡片的 `is-active` 与「使用中 / 使用」文本（避免异步拉取 catalog 造成延迟）。刷新后从 `GM_setValue` 重新注入已下载主题 CSS。
- 删除主题：清除 `GM_setValue`，移除对应 `<style>`；若删除的是当前应用主题，回退默认主题（apple），不残留缓存。
- 页面初始化：`init()` 先调用 `injectAllStoreThemeStyles()`（注入已下载主题 CSS 并设置 `data-urppp-skin`），再 `applyTheme`，避免首屏白闪。

---

## 八、投稿方式

### 方式一 · 合并至本仓库（集中式）

1. 将产物放入 `themes/<id>.css` 或 `plugins/<id>.js`，并按第二节在 `catalog.json` 添加条目。
2. 提交 Pull Request，审核通过后合并。
3. 由仓库维护者统一发版更新 catalog。

### 方式二 · 自建仓库（去中心化）

1. 第三方自行维护仓库，放置产物与自有 `catalog.json`（`entry` 与 `repo` 指向自身仓库）。
2. 在主插件「插件商店」→「仓库源」添加该源 URL，主插件合并拉取。官方源优先，同 id 冲突时官方源优先。
3. 产物、版本与更新由作者自行管理。
4. 自建源条目须附带 `signature`（签名校验）。安装第三方内容前，主插件会提示风险。

---

## 九、版本管理

- 版本号采用语义化 `x.y.z`，用于检查更新对比。
- 投稿或更新时，同步提升 catalog 的 `version` 与产物自带版本号，二者保持一致。
- 产物更新后，catalog 条目的 `version` 应同步提升，否则用户检查更新不生效。
