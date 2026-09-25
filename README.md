# Cookielet CMP — Google Tag Manager Template

The Cookielet CMP makes your website's use of cookies compliant with global
data privacy laws such as the GDPR, ePrivacy, CCPA, and LGPD. This Google Tag
Manager tag template connects [Cookielet](https://www.cookielet.com) to Google
Consent Mode v2.

## What the tag does

On every page where it fires, the tag:

<<<<<<< HEAD
1. **Sets Google tag options** with `gtagSet`:
   - `developer_id.dNWNkMD` — Cookielet's Google developer ID (always set)
   - `ads_data_redaction` — from the **Ads Data Redaction** checkbox
   - `url_passthrough` — from the **URL Pass Through** checkbox
2. **Sets the default consent state** with `setDefaultConsentState`, once per
   row of the **Default Consent Settings** table (see below). If **Wait For
   Time** is greater than 0, each call includes `wait_for_update`.
3. **Restores a saved choice.** If the `cmp_consent` cookie contains a status of
   `accept_all` or `deny_all` (plain or URL-encoded JSON, e.g.
   `{"status":"accept_all"}`), the tag calls `updateConsentState`:
   - `accept_all` → all seven consent types `granted`
   - `deny_all` → all consent types `denied` except `security_storage`
     (`granted`)

   Any other status, or no cookie, leaves the defaults unchanged.
4. **Loads the Cookielet script** from
   `https://cdn.cookielet.com/{Account Id}/{Site Id}/consent.js`. The tag
   reports success when the script loads and failure if it cannot be loaded.
   `consent.js` shows the banner and sends consent updates when the visitor
   makes or changes a choice, including per-category choices.

## Configuration

| Field | Required | Default | Description |
|-------|----------|---------|-------------|
| **Site Id** | Yes | — | Your Cookielet website ID |
| **Default Consent Settings** | No | — | Per-region default consent table (see below) |
| **Other Settings → Wait For Time** | No | `2000` | Milliseconds Google tags wait for the visitor's consent choice before firing. Must be 0 or more; `0` disables waiting. Recommended: 500–2000. |
| **Other Settings → Ads Data Redaction** | No | Off | When checked and *Advertisement Cookies* is disabled, Google's advertising tags remove advertising identifiers from requests and route traffic through cookieless domains. |
| **Other Settings → URL Pass Through** | No | Off | When checked and advertising consent is denied, Google tags pass ad-click information (such as gclid, dclid, gbraid, wbraid) to later pages through URL parameters instead of cookies. |
| **Account Id** | Yes | — | Your Cookielet account ID |
=======
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
>>>>>>> 8dedb40a029eaecc1d716aafbb09e9fdf1ce3f49

### Default Consent Settings

Each row in the table sets one default consent state. Choose **Enabled**
(`granted`) or **Disabled** (`denied`) for each column:

| Column | Consent Mode type(s) |
|--------|----------------------|
| Analytics Cookies | `analytics_storage` |
| Advertisement Cookies | `ad_storage` |
| Functional Cookies | `functionality_storage` and `personalization_storage` |
| Security Cookies | `security_storage` |
| Share user data with Google | `ad_user_data` |
| Use data for ads personalization | `ad_personalization` |
| Regions | Comma-separated region codes (e.g. `US, GB, DE`), or `All` (default) |

How rows are applied:

- A row with specific **Regions** applies only to those regions.
- A row with **Regions** set to `All` (or left empty) applies to all other
  visitors.
- If no row uses `All` — including when the table is empty — the tag applies
  this global default:

  | Type | State |
  |------|-------|
  | `ad_storage`, `analytics_storage`, `functionality_storage`, `personalization_storage`, `ad_user_data`, `ad_personalization` | `denied` |
  | `security_storage` | `granted` |

## Installation

1. In GTM, go to **Templates → Tag Templates → Search Gallery**, find
   **Cookielet CMP** and click **Add to workspace**.
   (Or: **Templates → New → ⋮ → Import** and select `template.tpl`.)
2. Go to **Tags → New** and choose the **Cookielet CMP** tag type.
3. Enter your **Site Id** and **Account Id** from your Cookielet dashboard.
4. Optionally add rows to **Default Consent Settings** for your regions.
5. Set the trigger to **Consent Initialization – All Pages**, so the defaults
   are set before any other tag fires.
6. Save and publish the container.

## Permissions

| Permission | Scope | Used for |
|------------|-------|----------|
| Injects scripts | `https://cdn.cookielet.com/*` | Loading `consent.js` |
| Accesses consent state | Write: `ad_storage`, `analytics_storage`, `functionality_storage`, `personalization_storage`, `security_storage`, `ad_user_data`, `ad_personalization` | Setting default and restored consent |
| Reads cookie values | `cmp_consent` only | Restoring a saved accept-all / deny-all choice |
| Writes to data layer | `ads_data_redaction`, `url_passthrough`, `developer_id.dNWNkMD` | Google tag settings via `gtagSet` |

## Links

- Homepage: <https://www.cookielet.com>
- Documentation: <https://www.cookielet.com/documentation>

## License

Apache License 2.0 — see [LICENSE](LICENSE).
