# 插件投稿示例：以「辅助插件」为例

本示例基于官方「辅助插件」（`plugins/urpppp.plugin.js`），演示插件投稿的完整要素（产物结构、注册接口、降级、暴露 API、持久化、生命周期、catalog 条目）。字段规范详见 [DEV_GUIDELINES.md](./DEV_GUIDELINES.md)。

---

## 目录

1. [插件能力说明](#一插件能力说明)
2. [catalog 条目](#二catalog-条目)
3. [插件实现（IIFE + 需装载）](#三插件实现iife--装载注册)
4. [降级与独立运行](#四降级与独立运行)
5. [暴露 API](#五暴露-api)
6. [持久化与生命周期](#六持久化与生命周期)
7. [投稿 Checklist](#七投稿-checklist)

---

## 一、插件能力说明

插件可托管主插件之外的**脚本能力**。辅助插件提供：登录助手（验证码识别、自动提交）、评教助手（自动评教 / 批量 / 保存）、会话保持（心跳 / 2FA）。

- 插件需要执行 JavaScript，catalog 中必须 `allowJS: true`。
- 插件通过主插件装载式加载：主插件创建 `window.__urpppPlugin`，插件检测到后调用 `register` 注册自身。

---

## 二、catalog 条目

```jsonc
{
  "id": "assist",
  "type": "plugin",
  "name": "辅助插件",
  "description": "登录助手 / 评教 / 会话保持 / 2FA",
  "version": "1.5.3",
  "author": "Chao_Lan",
  "repo": "https://github.com/chaolan2019/SCU-URP-plusplus",
  "minAPP": "1.9.0",
  "entry": [
    "https://raw.githubusercontent.com/chaolan2019/URP-plusplus-Repository/main/plugins/urpppp.plugin.js",
    "https://cdn.jsdelivr.net/gh/chaolan2019/URP-plusplus-Repository@main/plugins/urpppp.plugin.js",
    "https://gh-proxy.com/https://raw.githubusercontent.com/chaolan2019/URP-plusplus-Repository/main/plugins/urpppp.plugin.js"
  ],
  "allowJS": true
}
```

要点：`id` / `name` / `version` 必须与插件注册时一致；`allowJS` 必填；`entry` 提供多 URL 降级；`repo` 指向插件仓库。

---

## 三、插件实现（IIFE + 装载注册）

插件以自执行函数（IIFE）发布，检测主插件注入的装载接口 `window.__urpppPlugin`。

```js
(() => {
  'use strict';
  const VERSION = '1.5.3';                 // 与 catalog `version` 一致

  // …功能实现（登录 / 评教 / 会话保持）…
  function openLoginPanel() { /* … */ }
  function openEvalPanel() { /* … */ }
  function openSessionPanel() { /* … */ }

  const isPluginMode = typeof window.__urpppPlugin === 'object' && !!window.__urpppPlugin;
  if (isPluginMode) {
    try {
      window.__urpppPlugin.register({
        id: 'assist',               // 与 catalog `id` 一致
        type: 'plugin',
        name: '辅助插件',            // 与 catalog `name` 一致
        version: VERSION,           // 与 catalog `version` 一致
        subpanels: {                // 挂到主插件设置面板的子面板（可选）
          login:   { label: '登录助手', open: () => openLoginPanel() },
          eval:    { label: '评教助手', open: () => openEvalPanel() },
          session: { label: '会话保持', open: () => openSessionPanel() },
        }
      });
    } catch (_) { /* 装载失败时静默降级 */ }
  }
})();
```

**要求**：
- `id` / `name` / `version` 必须与 catalog 条目一致，否则商店管理页显示异常、检查更新不生效。
- `type` 固定为 `plugin`。
- `subpanels` 中每个子面板为 `{ label, open }`，`open` 为打开对应设置面板的回调。

---

## 四、降级与独立运行

插件可能脱离主插件运行（独立 userscript）。当 `window.__urpppPlugin` 不存在时，降级为独立模式，使用 GM 菜单命令：

```js
try {
  GM_registerMenuCommand('URP++辅助：打开设置说明', () => {
    alert('请启用 URP++ 主脚本，点击顶栏齿轮，在设置底部配置相关助手。');
  });
  GM_registerMenuCommand('URP++辅助：立即识别登录验证码', () => { resumeAutoLogin(); });
  GM_registerMenuCommand('URP++辅助：启动全自动评教', () => { startFullAutoEvaluation(); });
} catch (_) {}
```

---

## 五、暴露 API

为便于主插件、控制台或扩展调用，可将核心能力挂到全局命名空间：

```js
window.__urppppAssist = {
  version: VERSION,
  loginConf,
  runLogin: mainLogin,
  runEval: runEvaluationAssist,
  startFullAuto: startFullAutoEvaluation,
  stopFullAuto: stopFullAuto,
  injectSettings: injectSettingsPanel,
};
```

---

## 六、持久化与生命周期

- **持久化**：使用 `GM_getValue / GM_setValue`，存储键加命名空间前缀（如 `urpppp_assist_v1_...`）避免冲突。
- **依赖**：访问教务页 DOM / jQuery 时判空，并防重复绑定（用 `window.__xxxBound` 标记）。
- **装载 / 卸载**：主插件通过 `pluginManager`（`api.install / unregister / list / get / isEnabled`）管理。插件在 `unregister` 时清理 DOM、监听器与定时器。
- **设置面板**：通过主插件设置面板注入子面板（辅助插件用 `injectSettingsPanel` 注入「登录 / 评教 / 会话」配置项）。

---

## 七、投稿 Checklist

- [ ] catalog 为 `type: plugin`，`allowJS: true`。
- [ ] 插件检测 `window.__urpppPlugin` 并调用 `register`（`id` / `name` / `version` 与 catalog 一致）。
- [ ] `entry` 提供多 URL 降级，`repo` 指向插件仓库。
- [ ] 无装载接口时降级为独立模式（GM 菜单命令）。
- [ ] 持久化键带命名空间前缀，避免冲突。
- [ ] `unregister` 时清理 DOM / 监听 / 定时器。
- [ ] 更新时同步提升插件自带 `version` 与 catalog `version`。
