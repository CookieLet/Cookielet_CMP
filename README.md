# Cookielet CMP — Google Tag Manager Template

A GTM **custom tag template** that connects [Cookielet](https://www.cookielet.com)
to Google Tag Manager. It does two things on every page:

1. **Seeds Google Consent Mode v2 defaults** (region-aware) as early as possible,
   so tags that wait for consent hold until the visitor decides.
2. **Loads the Cookielet consent script** (`consent.js`) from the CDN, which
   renders the banner and owns every subsequent `consent` update.

## How it works

- Attach the tag to the **Consent Initialization – All Pages** trigger. That
  guarantees the default consent state is set before any other tag fires.
- The template calls `setDefaultConsentState(...)` for each row in the
  **Default Consent Settings** table. A row whose *Regions* is `All` applies a
  global fallback (`security_storage` granted, everything else denied); rows with
  specific regions apply per-region defaults.
- It sets `developer_id.dNWNkMD` (Cookielet's Google developer ID) along with
  the **Ads Data Redaction** and **URL Pass Through** options.
- If the visitor has already chosen **Accept all** or **Deny all**, the
  template reads that choice from the `cmp_consent` cookie and immediately
  calls `updateConsentState(...)`, so returning visitors' tags fire with the
  right consent without waiting for the banner script to load.
- It then injects the script:

  ```
  https://cdn.cookielet.com/{Account Id}/{Site Id}/consent.js
  ```

- After that, **`consent.js` is in charge.** It renders the banner, calls
  `gtag('consent','update',...)` when the visitor chooses, and re-applies
  per-category choices (stored in `localStorage` as `cmp_consent_meta`, which
  GTM's sandbox cannot read) on each page load.

## Configuration

| Field | Required | Default | Purpose |
|-------|----------|---------|---------|
| **Site Id** | ✅ | — | The Cookielet website id |
| **Account Id** | ✅ | — | The Cookielet account id |
| **Wait For Time** | | `2000` ms | How long tags wait for a consent update before firing; `0` disables waiting |
| **Ads Data Redaction** | | off | Sets `ads_data_redaction` |
| **URL PassThrough** | | off | Sets `url_passthrough` |

### Default Consent Settings (per-region table)

Each row maps UI columns to Consent Mode signals:

| Column | Consent Mode signal(s) |
|--------|------------------------|
| Analytics Cookies | `analytics_storage` |
| Advertisement Cookies | `ad_storage` |
| Functional Cookies | `functionality_storage`, `personalization_storage` |
| Security Cookies | `security_storage` |
| Share user data with Google | `ad_user_data` |
| Use data for ads personalization | `ad_personalization` |
| Regions | comma-separated region codes, or `All` for the global default |

Set each column to **Enabled** (`granted`) or **Disabled** (`denied`). Leave a
single row with *Regions* = `All` for a global default, and/or add rows targeting
specific regions (e.g. `US`, `GB-ENG`).

## Installation

1. In GTM, go to **Templates → New**, import `template.tpl`, and save.
2. Create a new tag from the **Cookielet CMP** template.
3. Fill in **Account Id** and **Site Id** (from your Cookielet dashboard).
4. Configure the default consent rows for your regions.
5. Set the trigger to **Consent Initialization – All Pages**.
6. Publish the container.

## Links

- Homepage: <https://www.cookielet.com>
- Documentation: <https://www.cookielet.com/documentation>

## License

Apache License 2.0 — see [LICENSE](LICENSE).
