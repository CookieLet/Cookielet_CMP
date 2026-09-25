# Cookielet CMP — Google Tag Manager Template

The Cookielet CMP makes your website's use of cookies compliant with global
data privacy laws such as the GDPR, ePrivacy, CCPA, and LGPD. This Google Tag
Manager tag template connects [Cookielet](https://www.cookielet.com) to Google
Consent Mode v2.

## What the tag does

On every page where it fires, the tag:

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
