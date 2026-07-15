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
