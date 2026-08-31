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
    "https://gitee.com/chaolan2026/URP-plusplus-Repository/raw/main/themes/organic.css"
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
  --success:#5F8B5F!important; --danger:#A05E5C!important; --warning:#B8924B!important; --info:#5A7A8F!important;
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
  --success:#8FB19A!important; --danger:#C9908D!important; --warning:#C8A76C!important; --info:#91AAB9!important;
}
```

**语义色变量（`--success` / `--danger` / `--warning` / `--info`）**：主插件所有「状态色」统一由这 4 个变量驱动（状态文字、按钮、徽章、alert、toast、成绩格、ztree 节点等）。**第三方主题应覆盖这 4 个变量**（亮暗两套），否则状态色会用主插件默认值（亮色 `#15803D`/`#B53434` 系、暗色自动变浅）。覆盖后状态色即跟随主题气质。

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

### 1.3b 主题可美化控件全清单（对照官方主题，避免漏项）

> 以下清单**以官方 4 套主题（flat / organic / brutal / neu）实际覆盖的全部选择器为准**（全量去重 587 项），按区域分组整理。写主题 CSS 时逐组对照，**每个分组至少覆盖核心控件**（标 ★ 的为必覆盖项），否则对应区域会"漏美化"（保持原生 ACE 样式）。所有规则均以 `html[data-urppp-skin="<id>"]` 为前缀（下面省略前缀）。

**A. 页面骨架与布局** ★

| 控件 | 选择器 | 备注 |
|---|---|---|
| 页面背景 | `body` ★ | 全局底色（`--bg`） |
| 内容容器 | `.main-container`, `.main-content`, `.main-content-inner`, `.page-content`, `#page-content-template` ★ | 主体内容区 |
| 清爽根 | `#urppp-clean-root` ★ | 清爽模式整体 |
| 顶栏 | `#navbar`, `.navbar`, `.navbar .btn`, `.navbar a.btn`, `.navbar button`, `.navbar-toggle` ★ | 导航条 |
| 顶栏用户区 | `.ace-nav>li>a`, `.ace-nav>li>a.btn`, `.nav-user-photo` | 右侧用户项 |
| 顶栏操作区 | `.header .right_top_oper .btn`, `.header .right_top_oper a` | 顶部操作按钮 |
| 侧栏 | `#sidebar`, `.sidebar` ★ | 左侧菜单 |
| 侧栏折叠 | `.menu-toggler`, `#menu-toggler`, `.urppp-sidebar-toggle` | 折叠开关 |
| 面包屑 | `.breadcrumbs`, `#breadcrumbs` | 路径条 |

**B. 卡片与面板** ★

| 控件 | 选择器 | 备注 |
|---|---|---|
| 通用卡片 | `.widget-box`, `.panel`, `.well`, `.modal-content`, `.urppp-card`, `.urppp-stat-card`, `.infobox`, `.thumbnail` ★ | 全部卡片容器 |
| 信息框 | `.infobox-container .infobox`, `.page-content .infobox` | 统计信息框 |
| 卡片头 | `.widget-header`, `.widget-title`, `.panel-heading`, `.urppp-card-header`, `.modal-header` ★ | 标题区 |
| 卡片体 | `.widget-main`, `.panel-body`, `.urppp-card-body`, `.modal-body` ★ | 内容区 |
| 用户信息 | `.profile-user-info`, `.profile-user-info-striped`, `.profile-info-row`, `.profile-info-name`, `.profile-info-value` ★ | 个人信息 |
| 仪表盘卡 | `.urppp-db-card`, `.urppp-db-panel`, `#urppp-dashboard .widget-box`, `#urppp-dashboard .urppp-card` | 仪表盘 |
| 设置面板 | `#urppp-settings-panel` ★ | 主插件设置（详见 G） |

**C. 按钮** ★

| 控件 | 选择器 | 备注 |
|---|---|---|
| 通用按钮 | `.btn`, `a.btn`, `button.btn`, `.btn:not(.btn-app)`, `#urppp-root .ubtn`, `#urppp-root .ut button`, `#urppp-nav-clean`, `#urppp-nav-cal` ★ | 默认按钮 |
| 主按钮 | `.btn-primary`, `.btn-info`, `#urppp-root .ubtn` ★ | 主要操作 |
| 状态按钮 | `.btn-success`, `.btn-warning`, `.btn-danger`, `.btn-info:hover`, `.btn-primary:hover` ★ | 成功/警告/危险 |
| 应用胶囊 | `.btn-app`, `a.btn-app`, `button.btn-app`, `#personalApplication .btn-app`, `#urppp-dashboard .btn-app` ★ | 首页应用入口 |
| 按钮变体 | `.btn-white`, `.btn-default`, `.btn-link`, `.btn-purple`, `.btn-minier`, `.btn-sm`, `.btn-xs` | 次要变体 |
| 按钮组 | `.btn-group>.btn`, `.btn-group-vertical` | 分组按钮 |
| 交互态 | `.btn:hover`, `.btn:active`, `.btn.active`, `.btn-app:hover`, `.btn-app:active` ★ | hover/active |
| 禁用 | `button:disabled`, `.btn.disabled`, `.urppp-theme-disabled` | 禁用态 |

**D. 表单** ★

| 控件 | 选择器 | 备注 |
|---|---|---|
| 输入框 | `input.form-control`, `select.form-control`, `textarea.form-control`, `.form-control` ★ | 基本输入 |
| type 全系列 | `input[type="text"]`, `input[type="search"]`, `input[type="number"]`, `input[type="password"]`, `input[type="email"]`, `input[type="tel"]`, `input[type="url"]`, `select`, `textarea`, `fieldset` ★ | 全部输入类型 |
| 占位符 | `input::placeholder`, `textarea::placeholder` | placeholder 色 |
| 聚焦 | 各输入 `:focus`, `input:focus`, `select:focus`, `textarea:focus` ★ | 聚焦边框 |
| 搜索框 | `#form-search .nav-search-input`, `#search-input`, `.nav-search .nav-search-input`, `input#search-input` ★ | 顶栏搜索 |
| chosen 下拉 | `.chosen-container-single .chosen-single`, `.chosen-container-active .chosen-single`, `.chosen-container-multi .chosen-choices`, `.chosen-container .chosen-drop`, `.chosen-results li`, `.chosen-results li.highlighted`, `.chosen-results li.result-selected` | 下拉选择器 |
| select2 | `.select2-drop` | select2 下拉 |

**E. 表格** ★

| 控件 | 选择器 | 备注 |
|---|---|---|
| 表格容器 | `.urppp-table-wrap`, `.table-responsive` | 外层 |
| 表格 | `.table`, `.table-bordered`, `.dataTable`, `table` ★ | 通用 |
| 表头 | `.table>thead>tr>th`, `.table>thead>tr>td`, `.table-bordered>thead>tr>th`, `.dataTable>thead>tr>th` ★ | 表头 |
| 表体 | `.table>tbody>tr>td`, `.table>tbody>tr>th`, `.table-bordered>tbody>tr>td`, `.dataTable>tbody>tr>td` ★ | 数据单元格 |
| 行悬停 | `.table-hover>tbody>tr:hover>td`, `.table>tbody>tr:hover>td`, `.dataTable>tbody>tr:hover>td` ★ | hover 行 |
| 状态背景 | `body .green_background`, `body .red_background`, `body .table>tbody>tr>td.green_background` 等 ★ | 课表/成绩状态色 |
| 通知表 | `body table.urppp-notice-table>tbody>tr`, `.urppp-notice-row:hover`, `.table-striped>tbody>tr:nth-of-type(odd/even)` | 通知/公告 |
| 通知卡 | `body .urppp-notice-card` | 通知卡片 |

**F. 状态与提示** ★

| 控件 | 选择器 | 备注 |
|---|---|---|
| 提示条 | `.alert`, `.alert-success`, `.alert-info`, `.alert-warning`, `.alert-danger` | 页面提示 |
| 徽章 | `.label`, `.badge` 及 `.label-success/info/warning/danger`, `.badge-success/info/warning/danger` ★ | 标签/计数 |
| 遮罩 | `#urppp-settings-mask`, `#urppp-clean-root .uc-mask`, `#urppp-update-changelog.open` | 弹层遮罩 |
| 弹出层 | `.dropdown-menu`, `.dropdown-menu>li>a`, `.dropdown-menu>li>a:hover`, `.popover` ★ | 下拉/气泡 |

**G. 设置面板（主插件）** ★

| 控件 | 选择器 | 备注 |
|---|---|---|
| 面板 | `#urppp-settings-panel` ★ | 整体 |
| 头部 | `#urppp-settings-panel .urppp-set-head`, `.urppp-set-title`, `.urppp-set-close` ★ | 标题/关闭 |
| 标签页 | `.urppp-set-tabs`, `.urppp-set-tab`, `.urppp-set-tab.ac`, `.urppp-set-tab.ac::after` ★ | 顶部 tab |
| 分区 | `.urppp-set-sec` ★ | 各设置区 |
| 模式切换 | `.urppp-set-modes`, `.urppp-set-mode`, `.urppp-set-mode.ac`, `.urppp-set-follow`, `.urppp-set-follow.ac`, `.urppp-set-follow-row` ★ | 亮暗/跟随 |
| 配色方案 | `.urppp-set-schemes`, `.urppp-set-scheme`, `.urppp-set-scheme.ac`, `.urppp-set-scheme-preview span` ★ | 色板选择 |
| 按钮 | `.urppp-set-btn`, `.urppp-set-btn:not(.ghost)` ★ | 设置按钮 |
| 辅助项 | `.urpppp-actions + .urpppp-status`, `.urpppp-entry-grid + .urpppp-tip`, `.urpppp-switches + .urpppp-grid`, `.urpppp-switches + .urpppp-sub`, `#urppp-set-auto-update + .urppp-set-tip`, `#urppp-set-check-update + #urppp-set-update-status`, `#urppp-set-clean-default + .urppp-set-tip` | 插件区/更新区提示 |

**H. 导航与分页** ★

| 控件 | 选择器 | 备注 |
|---|---|---|
| 侧栏菜单 | `.nav-list>li>a`, `.nav-list>li>a:hover`, `.nav-list>li.active>a`, `.urppp-nav-link`, `.urppp-nav-link:hover`, `.urppp-nav-item.active>a`, `.urppp-nav-item.open>a` ★ | 菜单项 |
| 标签页 | `.nav-tabs`, `.nav-tabs>li>a`, `.nav-tabs>li.active>a` ★ | 页面 tab |
| 分页 | `.pagination>li>a`, `.pagination>li>span`, `.pagination>.active>a`, `.urppp-page-chip`, `.urppp-page-chip-active` ★ | 分页器 |
| 主题圆点 | `.urppp-nav-dot`, `.urppp-nav-dot.ac`, `#urppp-dots span`, `#urppp-nav-theme .urppp-nav-dot`, `#urppp-clean-root .uc-top-theme .urppp-nav-dot` ★ | 顶栏/清爽主题切换 |
| 导航按钮 | `#urppp-nav-clean`, `#urppp-nav-cal`, `#urppp-nav-theme button`, `#urppp-nav-theme .urppp-nav-settings` | 清爽/校历入口 |

**I. 清爽模式（dashboard）** ★

| 控件 | 选择器 | 备注 |
|---|---|---|
| 桌面 | `#urppp-clean-root`, `.uc-desktop`, `.uc-col`, `.uc-shell-inner`, `.uc-bd > div` ★ | 布局容器 |
| 头部 | `.uc-top`, `.uc-brand`, `.uc-name`, `.uc-avatar`, `.uc-avatar img`, `.uc-top-actions .uc-btn`, `.uc-top-theme .urppp-nav-dot` ★ | 清爽头部 |
| 卡片 | `.uc-card`, `.uc-card:hover` ★ | 清爽卡片 |
| 按钮 | `.uc-btn`, `.uc-btn.primary`, `.uc-btn:not(.primary)`, `.uc-btn:hover`, `button.uc-btn` ★ | 清爽按钮 |
| 标签页 | `.uc-tabbar`, `.uc-tabbar button`, `.uc-tabbar button:hover`, `.uc-hd-tabs .uc-sa-tab`, `.uc-sa-tab.ac`, `.uc-sa-tab.ac::after`, `.uc-sa-more`, `.uc-sa-more:hover` ★ | 成绩分析标签 |
| 统计 | `.uc-gpa`, `.uc-metric`, `.uc-cd-chip`, `.uc-attr-pill` ★ | 绩点/学分 |
| 课表 | `.uc-lesson`, `.uc-lesson:hover`, `.uc-grid-cell`, `.uc-course-sub`, `[data-score]` ★ | 课程格 |
| 成绩 | `.uc-score-pane`, `.uc-score-pane h5`, `.uc-score-cell`, `.uc-score-cell.pass/.fail/.uneval`, `.uc-score-grid > *`, `#uc-score-wrap` ★ | 成绩区域 |
| 图表卡 | `.uc-sa-chart-card` | 成绩图表 |
| 用户档案 | `.uc-profile` | 清爽个人页 |
| 弹窗 | `.uc-modal`, `.uc-modal-hd` | 清爽弹窗 |
| 服务入口 | `.uc-svc`, `.uc-svc:hover`, `.uc-svc *`, `.uc-svc:nth-child(4n+1~3)` ★ | 应用服务 |
| 退出/刷新/设置 | `#uc-exit`, `#uc-refresh`, `#uc-settings`, `#urppp-clean-entry` | 清爽右上操作 |
| 成绩入口 | `#urppp-clean-root .uc-build-grid button` 及 hover | 构建网格 |

**J. 校历 / 课程日历（fc-*）**

| 控件 | 选择器 | 备注 |
|---|---|---|
| 日历容器 | `#urppp-left .fc`, `#urppp-left #main-calendar`, `#urppp-left .fc-view` | 校历 |
| 日历按钮 | `.fc .fc-button`, `.fc-button`, `.fc-button-group > *`, `.fc-state-default`, `.fc-today-button`, `.fc-next-button`, `.fc-prev-button`, `.fc button` | 日历工具按钮 |
| 日历标题 | `.fc .fc-toolbar h2`, `.fc-toolbar h2`, `#urppp-left .fc-toolbar h2` | 月份标题 |

**K. 链接与文字** ★

| 控件 | 选择器 | 备注 |
|---|---|---|
| 链接 | `a`, `a:link`, `a:visited`, `a:hover`, `a:focus` ★ | 全局链接 |
| 标题 | `h1`~`h5`, `.page-header` ★ | 标题字体/色 |
| 焦点环 | `a:focus-visible`, `button:focus-visible`, `[tabindex]:focus-visible` ★ | 键盘焦点 |

> **对照方法**：写完用「搜索」逐组确认 A~K 每个分组至少覆盖 1~2 个核心控件（标 ★ 项）。官方 neu.css 覆盖最全（133 组选择器），brutal 覆盖设置面板最全，flat 覆盖清爽模式细节最全，可对照其写法。完全没覆盖某分组 = 该区域测试时会露原生样式。

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
