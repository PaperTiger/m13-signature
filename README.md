# M13 Email Signature Builder

A single-page tool for creating on-brand M13 email signatures. Fill in your details,
watch the live preview update, and copy a signature that pastes cleanly into Outlook,
Gmail, and Apple Mail.

**Live:** https://papertiger.github.io/m13-signature/

## Features

- **Live preview** as you type.
- **Two layouts** — pick one and the preview switches instantly:
  - **Compact** — name and title on the left, M13 logo top-right, a rule, then all
    contact details on a single line.
  - **Banner** — stacked contact details on the left with the "We are brighter
    together." panel on the right.
- **One-click copy** — puts a formatted (rich-HTML) signature on the clipboard so it
  pastes with all styling intact. Also copies a plain-text fallback.
- **Copy HTML source** for anyone pasting into an HTML signature field.
- **Optional fields auto-hide** — leave the phone or job title blank and the line drops
  out, with spacing recalculated automatically.
- **Brand-locked** logo, banner panel, and `m13.co` website link, so every signature
  stays consistent. The logo and the banner panel both link to `m13.co` too.

## Fields

| Field | Notes |
| --- | --- |
| Full name | Required |
| Job title | Optional — leave blank to hide the line |
| Email | Required — becomes a `mailto:` link |
| Phone | Optional — becomes a click-to-call `tel:` link built from the number |
| Layout | Compact or Banner |

Copy is disabled until the name and a well-formed email are present.

## Why it survives paste into Outlook

Email clients rewrite `<p>` margins and `line-height` and ignore `border-radius`, which
is what makes pasted signatures look loose or square-cornered. This builder avoids all
of that:

- **Table-based layout** with spacing driven by `<td>` padding (respected on paste),
  not paragraph margins.
- **Explicit `line-height` + `mso-line-height-rule:exactly`** on every line.
- **No CSS `border-radius`.** The rounded corners on the banner panel and the contact
  icons are baked into the PNGs with transparent corners, so Outlook can't strip them.
- **Every `<img>` carries explicit `width`/`height`** as both attributes and inline
  styles, sized to each asset's true pixel ratio so nothing distorts:

  | Asset | Source | Rendered |
  | --- | --- | --- |
  | Logo | 238×116 | 80×39 |
  | Banner panel | 648×482 | 200×149 |
  | Contact icons | 64×64 | 24×24 |

- **Images are hosted PNGs** on the Webflow CDN. Email clients block data-URI images, so
  the preview and the copied output both reference the same absolute CDN URLs.

## Editing brand constants

All fixed values live as constants near the top of the `<script>` in `index.html`:

```js
var CDN        = "https://cdn.prod.website-files.com/6a1e5c828336f205b2137ec6/";
var LOGO       = CDN + "..._Logo.png";
var BANNER     = CDN + "..._image.png";
var ICON_MAIL  = CDN + "..._Icon_mail.png";
var ICON_PHONE = CDN + "..._Icon_phone.png";
var ICON_WEB   = CDN + "..._Icon_web.png";
var SITE_URL   = "https://www.m13.co";
```

If you re-export an asset, upload the new PNG to the Webflow assets and update the
matching constant. If its aspect ratio changes, update the `LOGO_W`/`LOGO_H`,
`BAN_W`/`BAN_H`, or `ICON` constants directly below to match, or the image will distort.

## The email domain is `m13.co`, not `m13.com`

`m13.com` redirects to `m13print.com` — an unrelated company — and has its own mail
server. Mail sent to a `@m13.com` address does **not** reach M13. The builder is locked
to `m13.co`, which is the live site and carries M13's Google Workspace MX records.

## Reference files

`M13_email-signature_1.html` and `M13_email-signature_2.html` are the original static
designs the builder was derived from, kept for reference. The builder is the source of
truth — prefer it over copying these by hand.

They are **not published**: `_config.yml` excludes them from the Pages build, so
`index.html` is the only page the site serves. They remain in the repo and in git
history. If you ever want them hosted again, drop them from the `exclude:` list.

## Local preview

The app is a single self-contained file with no build step. Serve the folder over HTTP
(rich clipboard copy needs a secure context — `localhost` counts):

```bash
python3 -m http.server 8000
# then open http://localhost:8000/
```

## Deployment

Hosted with **GitHub Pages** from the `main` branch, `/ (root)` folder
(Settings → Pages). Any push to `main` redeploys.

> Note: the sibling `incquery-signature` repo deploys from `develop`. This repo uses
> `main`. Worth aligning if the team standardises on one.
