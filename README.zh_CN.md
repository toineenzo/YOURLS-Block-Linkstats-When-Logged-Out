# block-details-while-not-login | 未登录时屏蔽详情页

[YOURLS](https://yourls.org/) 的插件，在未登录时屏蔽详情页（`yourls-infos.php`）。

> 🇬🇧 English version: [README.md](README.md)

> **v2.0.0 — 兼容 YOURLS 1.10+ 与 PHP 7.4 至 8.5**
>
> v1.x 用户：本版本修复了在 YOURLS 1.10+ 上由旧的 `require_auth` 钩子导致的「Unauthorized action or expired link」登录错误。原有的 `tao_*` 配置项会自动迁移，无需重新配置。

## 特性

### 多语言

| English | 中文 | Nederlands |
|:--:|:--:|:--:|
| ![英文界面](./imgs/1.png) | ![中文界面](./imgs/1_cn.png) | *2.0.0 新增 nl_NL* |

> 翻译通过标准的 YOURLS gettext 加载（`yourls_load_custom_textdomain`）。已包含：`en_US`（内建）、`zh_CN`、`nl_NL`。其他语言回落到英文。
>
> **添加新语言：** 复制 `languages/block-details-while-not-login.pot` 为 `block-details-while-not-login-<locale>.po`，翻译后执行 `msgfmt -o block-details-while-not-login-<locale>.mo block-details-while-not-login-<locale>.po`。

### 安全

| 曾经 | 现状 |
|:--:|:--:|
| ![曾经 — 公开访问](./imgs/2.png) | ![现状 — 已屏蔽](./imgs/3.png) |
| :x: 后台存在爆破和恶意请求风险 | :white_check_mark: 安全！|

### 自定义

- 可配置的 HTTP 状态码（401 / 403 / 404 / 410 / 451）
- 自定义标题和消息文本
- 可选的重定向 URL 与延迟
- 可选的内联 JavaScript 注入（自动包裹在 IIFE 中）
- 可切换 YOURLS 品牌头部
- 「恢复默认值」按钮

| ![英文界面](./imgs/1.png) | ![中文界面](./imgs/1_cn.png) |
|:--:|:--:|

> 图片上显示的是 5 秒后跳转到主页的例子。

## 安装

1. 下载本插件（或 `git clone` 此仓库）至 `user/plugins/block-details-while-not-login/`。
2. 在 YOURLS 后台 **管理插件** 中启用本插件。
3. 在插件侧边栏的 **block-details-while-not-login** 进行配置。

## 加固建议

即便启用本插件，访客仍可通过猜测 URL 找到 `/admin/` 登录表单。若需彻底消除该入口，请采用 ozh 在 [YOURLS PR #2747 #689047797 评论](https://github.com/YOURLS/YOURLS/pull/2747#issuecomment-689047797) 中提到的两步法：

1. 将 `admin/` 目录重命名为一个秘密名称，例如 `OMGSECRETURL/`。
2. 创建 `user/cache.php`（在插件加载之前会被极早地包含）：

   ```php
   <?php
   yourls_add_filter('admin_url', 'custom_admin_url');
   function custom_admin_url($url) {
       return str_replace('/admin/', '/OMGSECRETURL/', $url);
   }
   ```

这会把所有由 YOURLS 生成的后台 URL 重写为你的秘密名称，原有链接照常工作；爬虫和爆破程序无法在常见路径上找到登录表单。

## 兼容性

| YOURLS  | PHP            | 状态 |
|:-------:|:--------------:|:----:|
| 1.7+    | 7.4 / 8.0–8.5  | ✓ 已支持（v2.0.0） |
| 1.10+   | 8.0–8.5        | ✓ 推荐 |
| 1.10+   | （v1.x 插件）  | :x: 登录损坏 — 请升级到 v2.0.0 |

## 许可证

GPL-3.0。详见 [LICENSE](LICENSE)。

---

PS: YOURLS 最新版的汉化也是我维护的，可以访问 [我的翻译仓库](https://github.com/taozhiyu/yourls-translation-zh_CN)。
