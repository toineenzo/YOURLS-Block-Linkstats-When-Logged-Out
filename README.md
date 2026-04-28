# YOURLS plujgin - Block Linkstats when not logged in

A plugin for [YOURLS](https://yourls.org/) that blocks the URL details/stats page (`yourls-infos.php`) for unauthenticated visitors.

> 🇨🇳 中文版本请见 [README.zh_CN.md](README.zh_CN.md)

> **v2.0.0 — Compatible with YOURLS 1.10+ and PHP 7.4 through 8.5**
>
> v1.x users: this version fixes the *"Unauthorized action or expired link"* login error caused by the old `require_auth` hook on YOURLS 1.10+. Existing `tao_*` settings are migrated automatically — no reconfiguration required.

## Features

### Multilingual

| English | 中文 | Nederlands |
|:--:|:--:|:--:|
| ![English UI](./imgs/1.png) | ![Chinese UI](./imgs/1_cn.png) | *nl_NL added in 2.0.0* |

> Translations are loaded via standard YOURLS gettext (`yourls_load_custom_textdomain`). Shipped: `en_US` (built-in), `zh_CN`, `nl_NL`. Other locales fall back to English.
>
> **Adding a language:** copy `languages/block-details-while-not-login.pot` to `block-details-while-not-login-<locale>.po`, translate the strings, then `msgfmt -o block-details-while-not-login-<locale>.mo block-details-while-not-login-<locale>.po`.

### Safety

| Before | Now |
|:--:|:--:|
| ![Before — public details](./imgs/2.png) | ![After — blocked](./imgs/3.png) |
| :x: Risk of brute-force attacks and malicious requests | :white_check_mark: Safe! |

### Customization

- Configurable HTTP status code (401 / 403 / 404 / 410 / 451)
- Custom title and message text
- Optional redirect URL with configurable delay
- Optional inline JavaScript injection (wrapped in an IIFE)
- Toggle the YOURLS branding header
- "Reset to defaults" button

| ![English UI](./imgs/1.png) | ![Chinese UI](./imgs/1_cn.png) |
|:--:|:--:|

> The image shows an example of jumping to the home page after 5 seconds.

## Installation

1. Download the plugin (or `git clone` this repo) into `user/plugins/block-details-while-not-login/`.
2. Activate it in **Manage Plugins** in your YOURLS admin.
3. Visit the **block-details-while-not-login** entry in the plugin sidebar to configure.

## Hardening recommendation

Even with this plugin, a guest can still find your `/admin/` login form by guessing the URL. To remove that surface entirely, follow ozh's two-step trick from [YOURLS PR #2747 comment 689047797](https://github.com/YOURLS/YOURLS/pull/2747#issuecomment-689047797):

1. Rename your `admin/` directory to a secret name, e.g. `OMGSECRETURL/`.
2. Create `user/cache.php` (loaded extremely early, before plugins) with:

   ```php
   <?php
   yourls_add_filter('admin_url', 'custom_admin_url');
   function custom_admin_url($url) {
       return str_replace('/admin/', '/OMGSECRETURL/', $url);
   }
   ```

This rewrites every YOURLS-generated admin URL to your secret name so links keep working, while crawlers and brute-forcers can't find the login form at the conventional path.

## Compatibility

| YOURLS  | PHP            | Status |
|:-------:|:--------------:|:------:|
| 1.7+    | 7.4 / 8.0–8.5  | ✓ Supported (v2.0.0) |
| 1.10+   | 8.0–8.5        | ✓ Recommended |
| 1.10+   | (v1.x plugin)  | :x: Login broken — upgrade to v2.0.0 |

## License

GPL-3.0. See [LICENSE](LICENSE).

---

PS: I am also the maintainer of the latest Simplified Chinese translation of YOURLS. See [my translation repo](https://github.com/taozhiyu/yourls-translation-zh_CN).
