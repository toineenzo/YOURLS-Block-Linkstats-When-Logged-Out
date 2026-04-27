# Changelog

## 2.0.0

### Fixed

- **Critical:** Login on YOURLS 1.10+ no longer fails with `Unauthorized action or expired link`.
  - Root cause: the v1.x plugin hooked into the `require_auth` action and immediately called `yourls_is_valid_user()`. On YOURLS 1.10+, `yourls_redirect()` was changed to return instead of `exit` ([YOURLS#3189](https://github.com/YOURLS/YOURLS/issues/3189)), so the auth pipeline ran twice. The second pass computed the `admin_login` nonce against the now-defined `YOURLS_USER`, which never matches the placeholder `-1` baked into the form, so `yourls_verify_nonce()` killed the request.
  - Fix: switched to the `pre_yourls_infos` action (only fires on the stats page) and replaced `yourls_is_valid_user()` with a side-effect-free cookie check, so the auth flow runs exactly once.

### Added

- HTTP status code is now configurable (401 / 403 / 404 / 410 / 451).
- Optional **redirect URL** with configurable delay (0–60 seconds). Delay 0 = instant 302 redirect; nonzero = render message page first, then redirect via `<meta http-equiv="refresh">`.
- Toggle for the YOURLS branding header.
- **Reset to defaults** button on the settings page.
- Dutch (`nl_NL`) translation.
- Settings page CSS now uses CSS Grid for alignment and is responsive on mobile.
- Injected JavaScript is now wrapped in an IIFE to avoid leaking variables to the global scope.

### Changed

- Plugin metadata: name capitalised to *Block Details While Not Login*; description rewritten; version bumped to 2.0.0.
- All public functions now have explicit parameter and return types (`declare(strict_types=1)`).
- Option keys renamed from `tao_*` to `bdwnl_*`. **Existing settings are migrated automatically** the first time the new plugin loads — no reconfiguration required.
- Settings page slug remains `blocker_page` so existing bookmarks continue to work.
- Title and message are now HTML-escaped before output to harden against XSS via stored options.

### Compatibility

- Tested on **PHP 8.5.5** (`php -l` clean) and intended to support **PHP 7.4 through 8.5**.
- Tested against **YOURLS 1.10.3**.

## 1.2 (2023-06-14)

- Last release of the v1.x line.
- Known issue: incompatible with YOURLS 1.10+ — login dies with *"Unauthorized action or expired link"* once enabled.
