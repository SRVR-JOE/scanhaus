# SCANHAUS

**iPhone → continuous barcode scanner → Excel. A PIXEL HAUS shop tool.**

Point your phone at a rack, sweep slowly, and every barcode that enters frame
is decoded, deduped, and counted. Export a real `.xlsx` and AirDrop it to
your laptop, or import it into Eagle Eye / your carnet workbook.

No App Store, no Xcode, no build step — one HTML file.

## Deploy (one time, ~3 minutes)

iOS only allows camera access over **HTTPS**, so host it on GitHub Pages:

1. Push this repo to GitHub (`SRVR-JOE/scanhaus`).
2. Repo → **Settings → Pages → Source: Deploy from a branch → main / root**.
3. Open `https://srvr-joe.github.io/scanhaus/` on the iPhone.
4. Safari share sheet → **Add to Home Screen** → launches fullscreen like an app.

Any other HTTPS host works too (Netlify drop, Cloudflare Pages, a venue
server with a cert). Plain `http://` on shop LAN will NOT get camera access.

## Use

- **START SCAN** — grant camera access once. Sweep the rack ~18 in from
  labels. Green flash + beep = new code captured. Repeat reads of the same
  code just bump its count (1.8 s debounce).
- **Zoom slider / Torch** appear automatically if the camera exposes them
  (iOS 17+ Safari exposes zoom on most iPhones).
- **Manual entry** for scuffed/unreadable labels.
- **Export .xlsx** — downloads to Files. **Share** — AirDrop the sheet
  straight to the laptop.
- Session persists in localStorage — a Safari reload or accidental close
  doesn't lose the rack. **Clear** starts the next rack.

Decodes: Code 128, Code 39, EAN-13/8, UPC-A/E, ITF, Codabar, QR, DataMatrix
(ZXing multi-format, TRY_HARDER enabled).

## Spreadsheet columns

| Barcode | Type | Scans | First Seen | Last Seen |

`Scans` is a sanity signal: a code seen 6 times across a sweep is a solid
read; a code seen once at the edge of frame deserves a re-check.

## Notes / limits

- Decoder + SheetJS load from CDN, so first launch needs internet. After
  that Safari caches them; for fully-offline shop use, vendor the two JS
  files into the repo and swap the `<script src>` URLs to local paths.
- 1D barcodes (Code 128/39) decode best roughly parallel to the screen's
  horizontal axis — sweep with the phone in the label's orientation.
- Glare off laminated labels is the #1 miss cause; kill the front-of-rack
  worklight or angle slightly off-axis.

## Stack

- [@zxing/library](https://github.com/zxing-js/library) — multi-format decode in the browser
- [SheetJS](https://sheetjs.com) — `.xlsx` generation client-side
- Screen Wake Lock API — phone stays awake mid-sweep
- Web Share API — AirDrop the xlsx directly

© PIXEL HAUS LLC. All rights reserved.

## Custom scan sound

New-code scans say **"OH YEAH"** via text-to-speech. Drop a clip named
`ohyeah.mp3` in the repo root and the app uses it automatically instead.

## Send to Teams

Two ways:

1. **Share button** (sends the actual .xlsx): iOS share sheet → Teams app →
   pick chat/channel. Works with Outlook/OneDrive too. No setup.
2. **TEAMS button** (posts the code list as a card to one channel, one tap):
   - In Teams: channel → **⋯ → Workflows → "Post to a channel when a webhook
     request is received"** → finish the wizard → copy the URL.
     Microsoft guide: https://support.microsoft.com/en-us/office/create-incoming-webhooks-with-workflows-for-microsoft-teams-8ae491c7-0394-4861-ba59-055e33f75498
   - In SCANHAUS: tap **TEAMS**, paste the URL once (saved on the phone).
     Tap TEAMS with zero codes scanned to change the URL later.
   - Webhooks post messages/cards only — Microsoft doesn't allow file upload
     via webhook, so the xlsx itself still travels via Share.
   - Corporate tenants can disable Workflows creation; if the option is
     missing, that's an IT policy thing — the Share path always works.

## One-tap DM to a coworker

The **→ [name]** button deep-links into your Teams chat with a saved person,
scan list pre-typed — hit send in Teams. First tap asks for their name and
work email (saved on the phone); tap with an empty scan list to change who.
Deep links can't attach files (Microsoft limitation) — for the xlsx, use
Share → Teams: after a couple sends, iOS pins that person in the share
sheet's suggestion row for true one-tap file sends.

## Decode engines (v0.6)

| Engine | What | Cost | Where |
|---|---|---|---|
| **ZXING** | zxing-wasm, default | free | PWA |
| **STRICH** | commercial web SDK, big step up on hard labels | free trial → sub | PWA, ENGINE button |
| **NATIVE** | Google ML Kit on the Neural Engine, best available | free (needs Apple dev setup) | Capacitor iOS build, auto |

**STRICH trial:** https://strich.io → Start free trial → Customer Portal →
create a license key **scoped to `https://srvr-joe.github.io`** → in the app,
tap ENGINE until it reads STRICH, start a scan, paste the key (asked once).
Also worth benchmarking: Scandit (https://www.scandit.com/products/barcode-scanning/,
enterprise pricing) and Dynamsoft (https://www.dynamsoft.com/barcode-reader/overview/,
30-day trial) — STRICH is integrated here because it's the affordable one
built specifically for web apps.

**Native build:** see `docs/NATIVE.md`.
