# 投稿示例：以「自然有机」与「辅助插件」为例

本示例演示主题与插件两种投稿的完整要素（产物、catalog 条目、卡片样式、能力声明、仓库组织、自建源）。字段规范详见 [DEV_GUIDELINES.md](./DEV_GUIDELINES.md)。

---

## 目录

1. [主题投稿示例（natural organic）](#一主题投稿示例natural-organic)
2. [插件投稿示例](#二插件投稿示例)
3. [投稿者仓库组织](#三投稿者仓库组织)
4. [自建源（去中心化）示例](#四自建源去中心化示例)
5. [投稿 Checklist](#五投稿-checklist)

---

## 一、主题投稿示例（natural organic）

### 1.1 主题能力声明

| 能力 | 值 | 说明 |
|---|---|---|
| `dark` | `true` | 支持暗色模式；须提供暗色变体，主插件启用暗色开关 |
| `dynamic` | `false` | 不支持动态配色（种子色）；主插件屏蔽种子色选项，主题无需提供 |
| `palettes` | `false` | 不支持固定调色板；主插件屏蔽调色板项 |

能力声明必须与主题实际实现一致。`dynamic: false` 的主插件不会显示种子色内容。

### 1.2 catalog 条目

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
  "palettes": false,
  "cardCss": "…"   // 官方独立主题含完整 cardCss（亮/暗 + hover + 主按钮/仓库/删除，见 1.4）。主插件不再内置独立主题卡片样式，统一由 catalog cardCss 驱动
}
```

> 官方独立主题（flat / organic / brutal / neu）的 catalog 条目**含** `cardCss`，且已**从主插件脱离**——主插件不再内置这四套卡片样式，统一由 catalog `cardCss` 驱动（主插件仅保留 apple / editorial 内置）。第 1.4 节展示完整 cardCss 写法（所有主题必填，须覆盖主按钮/仓库/删除 + hover + 暗色变体）。

### 1.3 主题产物（organic.css）

产物仅注入 CSS。所有规则以 `html[data-urppp-skin="organic"]` 前缀限定作用域。

（1）**变量 token（亮色）**

```css
html[data-urppp-skin="organic"]{
  --radius:22px!important; --radius-sm:14px!important;
  --shadow:0 2px 10px rgba(92,64,51,.06)!important;
  --bg:#FAF6F1!important; --surface:#FFFCF7!important; --input-bg:#F3EDE4!important;
  --text:#3F2E24!important; --text-secondary:#6B5346!important; --text-muted:#8A7364!important;
  --border:#E7E0D6!important; --border-focus:#8B9D77!important;
  --primary:#5C4033!important; --primary-hover:#4A3329!important; --ring:rgba(92,64,51,.16)!important;
}
```

（2）**暗色变体**（`dark: true` 必提供）

```css
html[data-urppp-skin="organic"].urppp-theme-dark,
html.urppp-theme-dark[data-urppp-skin="organic"]{
  --bg:#1C1712!important; --surface:#2A221B!important; --input-bg:#342A22!important;
  --text:#F5EDE4!important; --text-secondary:#D2C0B0!important; --text-muted:#A89080!important;
  --border:#4A3B30!important; --border-focus:#A3B58A!important;
  --primary:#C4A484!important; --primary-hover:#D4B896!important; --ring:rgba(196,164,132,.22)!important;
  --shadow:0 8px 24px rgba(0,0,0,.4)!important;
}
```

（3）**背景纹理**（可选）

```css
html[data-urppp-skin="organic"] body,
html[data-urppp-skin="organic"] #urppp-clean-root{
  background-color:var(--bg)!important;
  background-image:url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='2' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.035'/%3E%3C/svg%3E")!important;
  background-attachment:fixed!important;
}
```

（4）**组件规则**（卡片 / 按钮 / 输入 / 导航点 / 标签页 / 评分 / 字体）

```css
html[data-urppp-skin="organic"] #urppp-clean-root .uc-card,
html[data-urppp-skin="organic"] #urppp-clean-root .uc-modal,
html[data-urppp-skin="organic"] #urppp-clean-root .uc-score-pane{
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
html[data-urppp-skin="organic"] input.form-control,
html[data-urppp-skin="organic"] #urppp-root .ui{
  border-radius:999px!important;
  border:1px solid var(--border)!important;
  background:var(--input-bg)!important;
  color:var(--text)!important;
}
html[data-urppp-skin="organic"] .urppp-nav-dot,
html[data-urppp-skin="organic"] #urppp-clean-root .uc-top-theme .urppp-nav-dot{
  border-radius:50%!important;
}
html[data-urppp-skin="organic"] h1,
html[data-urppp-skin="organic"] #urppp-clean-root .uc-brand{
  font-family:Georgia,"Noto Serif SC","Songti SC","Times New Roman",serif!important;
}
```

### 1.3b 主题可美化控件全清单（对照检查，避免漏项）

> 以下是从主插件全部主题化组件中整理的**完整控件清单**（参考官方 neu.css，覆盖最全）。写主题 CSS 时逐项对照，遗漏会导致对应区域"漏美化"。所有选择器均以 `html[data-urppp-skin="<id>"]` 为前缀（下面省略前缀书写）。

**A. 页面骨架**

| 控件 | 选择器 | 说明 |
|---|---|---|
| 页面背景 | `body` | 全局底色（变量 `--bg`） |
| 内容容器 | `.main-container`, `.main-content`, `.main-content-inner`, `.page-content`, `#page-content-template` | 主体内容区背景/内边距 |
| 清爽模式根 | `#urppp-clean-root` | 清爽模式整体容器 |
| 顶栏 | `#navbar`, `.navbar` | 导航条背景/高度 |
| 侧栏 | `#sidebar`, `.sidebar` | 左侧菜单栏 |
| 面包屑 | `.breadcrumbs`, `#breadcrumbs` | 顶部路径条 |

**B. 卡片与面板**

| 控件 | 选择器 | 说明 |
|---|---|---|
| 通用卡片 | `.widget-box`, `.panel`, `.well`, `.modal-content`, `.urppp-card`, `.urppp-stat-card`, `.infobox` | 页面上所有卡片容器 |
| 清爽卡片 | `#urppp-clean-root .uc-card` | 清爽模式卡片 |
| 卡片头 | `.widget-header`, `.panel-heading`, `.urppp-card-header`, `.modal-header`, `.uc-hd` | 卡片标题区 |
| 卡片体 | `.widget-main`, `.panel-body`, `.urppp-card-body`, `.modal-body` | 卡片内容区 |
| 用户信息 | `.profile-user-info`, `.profile-user-info-striped`, `.profile-info-row`, `.profile-info-name`, `.profile-info-value` | 个人信息面板 |
| 设置面板 | `#urppp-settings-panel`, `.urppp-set-sec` | 主插件设置面板 |

**C. 按钮**

| 控件 | 选择器 | 说明 |
|---|---|---|
| 通用按钮 | `.btn:not(.btn-app)`, `#urppp-root .ubtn`, `#urppp-nav-clean`, `#urppp-nav-cal` | 默认按钮 |
| 主按钮 | `.btn-primary:not(.btn-app)`, `.btn-info:not(.btn-app)` | 主要操作按钮 |
| 成功/警告/危险 | `.btn-success`, `.btn-warning`, `.btn-danger` | 状态按钮 |
| 应用胶囊按钮 | `.btn-app`, `#personalApplication .btn-app` | 首页应用入口胶囊 |
| 禁用态 | `button:disabled`, `.btn.disabled`, `.urppp-theme-disabled` | 禁用样式 |
| hover/active | 各按钮的 `:hover` / `:active` | 交互反馈必写 |

**D. 表单**

| 控件 | 选择器 | 说明 |
|---|---|---|
| 输入框 | `input.form-control`, `select.form-control`, `textarea.form-control`, `input[type="text"]`, `input[type="search"]` 等 | 输入/选择/文本域 |
| placeholder | `input::placeholder`, `textarea::placeholder` | 占位符颜色 |
| 聚焦 | `input.form-control:focus` 等 | 聚焦边框（`--border-focus`） |
| 下拉菜单 | `.dropdown-menu`, `.popover`, `.chosen-drop`, `.select2-drop` | 下拉面板 |
| 下拉项 | `.dropdown-menu>li>a`, `.chosen-results li` | 下拉选项 |

**E. 表格**

| 控件 | 选择器 | 说明 |
|---|---|---|
| 表格容器 | `.urppp-table-wrap`, `.table-responsive` | 表格外层 |
| 表格 | `.table`, `.table-bordered`, `.dataTable` | 通用表格 |
| 表头 | `.table>thead>tr>th` | 表头单元格 |
| 表体 | `.table>tbody>tr>td` | 数据单元格 |
| 行悬停 | `.table-hover>tbody>tr:hover>td` | 行 hover |
| 课程表 | `#courseTable`, `#courseTable th/td`, `.class_div.box_font`, `div[class*="div-kcb"]` | 课表单元格 |
| 通知表 | `table.urppp-notice-table` 及行 hover | 通知/公告表 |
| 清爽成绩表 | `#urppp-clean-root table.uc-table th/td`, `tbody tr:hover`, `tr.is-on` | 清爽成绩表格 |

**F. 状态与提示**

| 控件 | 选择器 | 说明 |
|---|---|---|
| 提示条 | `.alert`, `.alert-success/info/warning/danger` | 页面提示 |
| 徽章 | `.label`, `.badge` 及各状态变体 | 标签/计数 |
| 遮罩 | `#urppp-settings-mask`, `.uc-mask`, `#urppp-update-changelog.open` | 弹层遮罩 |

**G. 导航与分页**

| 控件 | 选择器 | 说明 |
|---|---|---|
| 侧栏链接 | `.nav-list>li>a`, `.urppp-nav-link` 及 hover/active | 侧栏菜单项 |
| 分页 | `.pagination>li>a/span`, `.urppp-page-chip`, `.urppp-page-chip-active` | 分页器 |
| 主题圆点 | `.urppp-nav-dot`, `#urppp-dots span` 及 `.ac` | 顶栏主题切换圆点 |

**H. 清爽模式（dashboard）专属**

| 控件 | 选择器 | 说明 |
|---|---|---|
| 桌面网格 | `#urppp-clean-root .uc-desktop`, `.uc-col` | 清爽桌面布局 |
| 统计卡 | `#urppp-dashboard .urppp-stat-card`, `.uc-stat`, `.uc-metric` | 数据统计卡 |
| 头部 | `.uc-top`, `.uc-brand`, `.uc-name`, `.uc-avatar` | 清爽头部 |
| 标签页 | `.uc-hd-tabs .uc-sa-tab`, `.uc-sa-tab.ac`, `.uc-sa-more` | 成绩分析标签 |
| GPA/徽章 | `.uc-gpa`, `.uc-cd-chip`, `.uc-attr-pill` | 绩点/学分徽章 |
| 课程格 | `.uc-lesson`, `.uc-grid-cell`, `.uc-course-sub` | 课表格子 |
| 评分格 | `.uc-score-cell` 及 `.pass/.fail/.uneval` | 成绩状态格 |
| 预约槽 | `.uc-slot` 及 `.free/.kind-course/.kind-exam/.kind-lab/.kind-borrow` | 空教室预约槽 |
| 服务入口 | `.uc-svc`, `.uc-services` | 应用服务入口 |

**I. 其他**

| 控件 | 选择器 | 说明 |
|---|---|---|
| 标题 | `h1`~`h4`, `.page-header`, `.urppp-set-title` | 页面/区块标题（字体） |
| 焦点环 | `a:focus-visible`, `button:focus-visible`, `[tabindex]:focus-visible` | 键盘焦点可见性 |
| 响应式 | `@media (max-width:900px)` 内 `#sidebar`, `.uc-shell` 等 | 移动端侧栏/清爽布局 |

> **对照方法**：写完后用「搜索」逐项确认你的 CSS 覆盖了 A~I 每个分组（至少每个分组 1~2 个核心控件）。官方 neu.css 覆盖最全（133 组），可对照其写法。

---

### 1.4 卡片样式（cardCss，必填且须完整）

> **卡样式三种来源（主插件按优先级取）**：
> 1. **catalog 条目的 `cardCss` 字段**（自建源/投稿）——下载时缓存到本地；
> 2. **主题 CSS 文件内自带的 `.urppp-skin-card[data-skin="<id>"]` 规则段**（本地导入）——导入时自动提取缓存，优先级高于自动生成；
> 3. **自动生成默认卡样式**（从主题变量 `--surface`/`--text`/`--border`/`--primary` 推导亮暗两套）——未提供 cardCss 时的兜底，保证卡不裸奔。
>
> 本地导入时**推荐在 CSS 文件末尾直接写卡样式段**（等效于 catalog 的 cardCss），导入即生效且完全由你掌控：

作用域 `.urppp-skin-card[data-skin="<id>"]`。cardCss 须覆盖：卡片底色/文字色、主按钮 `.urppp-skin-apply`、**次要按钮 `.urppp-store-repo` / `.urppp-store-del`（仓库/删除，须跟主按钮同主题色）**、hover 动效、`is-current`，以及 `html.urppp-theme-dark` 暗色变体。以下值与 catalog 中的实际 `cardCss` 一致：

```css
/* 亮色 */
.urppp-skin-card[data-skin="organic"]{
  background:#FAF6F1; color:#5C4033;
  border:1px solid #E8DFD2; border-radius:22px;
  box-shadow:none; padding-bottom:52px;
}
.urppp-skin-card[data-skin="organic"] .urppp-skin-name,
.urppp-skin-card[data-skin="organic"] .urppp-skin-desc{ color:inherit; }
.urppp-skin-card[data-skin="organic"] .urppp-skin-apply,
.urppp-skin-card[data-skin="organic"] .urppp-store-repo,
.urppp-skin-card[data-skin="organic"] .urppp-store-del{
  background:#FFFCF7; color:#5C4033;
  border:1px solid #8B9D77; border-radius:999px; box-shadow:none; transition:background 150ms,color 150ms;
}
.urppp-skin-card[data-skin="organic"] .urppp-skin-apply:hover,
.urppp-skin-card[data-skin="organic"] .urppp-store-repo:hover,
.urppp-skin-card[data-skin="organic"] .urppp-store-del:hover{ background:#5C4033; color:#fff; }
.urppp-skin-card[data-skin="organic"] .urppp-skin-apply.is-current{ background:#5C4033; color:#fff; border-color:#5C4033; }

/* 暗色变体（dark: true 时必提供，且同样覆盖次要按钮） */
html.urppp-theme-dark .urppp-skin-card[data-skin="organic"]{
  background:#2A221B; color:#F5EDE4; border-color:#4A3B30;
}
html.urppp-theme-dark .urppp-skin-card[data-skin="organic"] .urppp-skin-name,
html.urppp-theme-dark .urppp-skin-card[data-skin="organic"] .urppp-skin-desc{ color:inherit; }
html.urppp-theme-dark .urppp-skin-card[data-skin="organic"] .urppp-skin-apply,
html.urppp-theme-dark .urppp-skin-card[data-skin="organic"] .urppp-store-repo,
html.urppp-theme-dark .urppp-skin-card[data-skin="organic"] .urppp-store-del{
  background:#2B231D; color:#B9C99A; border-color:#6F8F52;
}
html.urppp-theme-dark .urppp-skin-card[data-skin="organic"] .urppp-skin-apply:hover,
html.urppp-theme-dark .urppp-skin-card[data-skin="organic"] .urppp-store-repo:hover,
html.urppp-theme-dark .urppp-skin-card[data-skin="organic"] .urppp-store-del:hover{ background:#5C4033; color:#fff; }
```

> **重要**：cardCss 若漏写 `.urppp-store-repo` / `.urppp-store-del` 或 hover 动效，脱离主插件内置后「仓库/删除」按钮会显示默认颜色、且无悬停动画（cardCss 驱动下没有内置兜底）。务必完整书写，可对照 catalog 官方主题的 `cardCss`。

要点：卡片需预留 `padding-bottom:52px` 给底部按钮；`hover` / `is-current` **必写**（不是可选）；支持暗色时暗色变体同步覆盖卡片底、文字、主按钮与次要按钮。

---

## 二、插件投稿示例

插件投稿的完整规范与示例（注册接口、降级、暴露 API、生命周期、catalog 条目）见 [PLUGIN_EXAMPLE.md](./PLUGIN_EXAMPLE.md)。

---

## 三、投稿者仓库组织

以官方仓库为例（主题产物 + 插件产物 + catalog + 说明文档）：

```
your-repo/
├── catalog.json           # 商店清单（theme + plugin 条目）
├── themes/
│   ├── organic.css        # 主题产物
│   └── …
├── plugins/
│   └── urpppp.plugin.js   # 插件产物
├── README.md              # 仓库说明
└── DEV_GUIDELINES.md      # （可选）引用的规范
```

`catalog.json` 的 `entry` 与 `repo` 指向本仓库。若走集中式（PR 到官方仓库），产物放入官方仓库对应目录，并在官方 `catalog.json` 添加条目。

---

## 四、自建源（去中心化）示例

自建源使用独立的 `catalog.json`，条目 schema 与官方一致，`entry` / `repo` 指向自身仓库，并携带 `signature`：

```jsonc
{
  "id": "mytheme",
  "type": "theme",
  "name": "我的主题",
  "description": "……",
  "version": "1.0.0",
  "author": "me",
  "repo": "https://github.com/me/my-repo",
  "entry": [
    "https://raw.githubusercontent.com/me/my-repo/main/themes/mytheme.css"
  ],
  "dark": true,
  "cardCss": "…",
  "signature": "sha256:…"
}
```

投稿者在主插件「插件商店」→「仓库源」添加该源 URL 后，主插件合并拉取。官方源优先，同 `id` 冲突时官方源优先。安装第三方内容前，主插件会提示风险。

---

## 五、投稿 Checklist

**主题（theme）**
- [ ] 产物所有规则以 `html[data-urppp-skin="<id>"]` 为前缀。
- [ ] 支持暗色（`dark: true`）时提供 `urppp-theme-dark` 暗色变体，覆盖色板变量与组件。
- [ ] 按第 1.3b 节控件全清单逐项对照 A~I 分组（骨架/卡片/按钮/表单/表格/状态/导航/清爽模式/其他），每个分组至少覆盖核心控件。
- [ ] 卡片样式提供完整 cardCss（三种来源任选：catalog 字段 / CSS 文件内 `.urppp-skin-card` 段 / 自动生成兜底；推荐前两种完全掌控）。
- [ ] catalog 条目包含 `type` / `name` / `description` / `version` / `author` / `repo` / `entry`。
- [ ] `entry` 至少 2 个 URL 保证降级可用。
- [ ] 第三方主题在 `cardCss` 提供卡片底色与主按钮样式（不含次要按钮）。
- [ ] 能力声明（`dark` / `dynamic` / `palettes`）与实际实现一致。
- [ ] 独立主题（官方 flat/organic/brutal/neu + 第三方）均在 `cardCss` 提供完整卡片样式（含主按钮/仓库/删除色、hover 动效、is-current、暗色变体）。

**插件（plugin）**
- [ ] catalog 含 `type: plugin` 与 `allowJS: true`。
- [ ] 插件注册字段（名称 / 描述 / 作者 / 版本 / 仓库）与 catalog 一致。
- [ ] `entry` 多 URL 降级，`repo` 指向插件仓库。

**自建源（去中心化）**
- [ ] 产物 + 自有 `catalog.json` 存放于自建仓库。
- [ ] 条目携带 `signature`（签名校验）。
- [ ] `entry` / `repo` 指向自建仓库。
