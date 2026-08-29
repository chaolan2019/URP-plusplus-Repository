# contribute/ —— 投稿快捷工具

本目录提供可直接复制的起点文件与自检脚本。**字段规范、投稿流程、卡片样式与暗色适配的完整说明请读 [DEV_GUIDELINES.md](../DEV_GUIDELINES.md)**，主题投稿全要素示例见 [THEME_EXAMPLE.md](../THEME_EXAMPLE.md)。

| 文件 | 用途 |
|---|---|
| `theme-template.css` | 主题 CSS 骨架：全局替换 `<your-theme-id>` 即可开工，含全部必填变量 |
| `catalog-item.template.json` | catalog 条目模板：填入你的信息后合并进 catalog.json |
| `check.mjs` | 自检脚本：投稿前本地校验，全绿再提 PR |

## 自检用法

```bash
node contribute/check.mjs 你的-catalog.json 你的主题.css
```

校验内容：catalog 顶层字段、条目必填项、id 规范（小写字母/数字/连字符，不与官方 flat / organic / brutal / neu / assist 重名）、版本语义化、entry 必须 https 多源、cardCss 必须含 `[data-skin="<id>"]` 选择器、主题 CSS 的 `data-urppp-skin` 前缀与 13 个必填变量。

> 注意：本脚本面向**投稿者**。对官方 catalog 本体运行会因官方 id 重名报错，属预期。

## 投稿渠道

- **官方源（PR）**：产物放 `themes/<id>.css`，在 `catalog.json` 加条目，提 PR 并附自检输出。
- **自建源（去中心化）**：自建仓库放产物与你的 `catalog.json`，在主插件「设置 → 商店 → 仓库源」添加地址即可，无需审核。

详见 [DEV_GUIDELINES.md 的双轨投稿方式](../DEV_GUIDELINES.md)。
> **局域网源提示**：若自建源部署在内网地址（如 192.168.x.x），用户首次添加时 Tampermonkey 会弹一次连接授权，允许后不再询问；回环地址（127.0.0.1 / localhost）已内置白名单，直接可用。

## 自建源收录进官方目录

自建源稳定运行后，可以申请收录进官方「仓库源」界面的收录列表，让全部用户一键添加：

1. 确保你的 \catalog.json\ 通过自检（含 \signature\ 公钥更佳，安装前可自动验签）；
2. 编辑本仓库根目录的 \sources.json\，在 \sources\ 数组追加一条：\{ "id": "唯一id", "name": "源名称", "author": "署名", "url": "https://…/catalog.json", "description": "一句话简介" }\；
3. 提交 PR 并附自检输出与源地址可访问性说明，审核合并后即出现在所有用户的仓库源界面。
