# SCANHAUS Native (iOS) — Capacitor + ML Kit

The native build swaps the web decoder for **Google ML Kit barcode scanning**
running on the iPhone's Neural Engine — the same class of decoder as
commercial scanning apps. The app auto-detects when it's running natively
and uses ML Kit; the ENGINE button is bypassed.

Plugin: https://github.com/capawesome-team/capacitor-mlkit/tree/main/packages/barcode-scanning
Capacitor iOS docs: https://capacitorjs.com/docs/ios

## Requirements (no way around these — Apple's rules)

- A **Mac** with Xcode 15+ (App Store, free) + CocoaPods (`sudo gem install cocoapods`)
- An Apple ID. Two tiers:
  - **Free Apple ID**: install on your own iPhone via Xcode, build expires
    every 7 days (fine for testing)
  - **Apple Developer Program, $99/yr** (https://developer.apple.com/programs/):
    TestFlight distribution, 1-year installs, share with coworkers

No Mac? GitHub Actions has free macOS runners for public repos and can
produce builds, but installing on-device still requires Apple signing —
the $99 account is unavoidable for anything durable.

## Build steps (on the Mac)

```bash
git clone https://github.com/SRVR-JOE/scanhaus && cd scanhaus
npm install
npx cap add ios
npx cap sync ios
```

Add the camera permission string — edit `ios/App/App/Info.plist`, inside the
top-level `<dict>`:

```xml
<key>NSCameraUsageDescription</key>
<string>SCANHAUS uses the camera to scan barcodes.</string>
```

Then:

```bash
npx cap open ios
```

In Xcode: select the **App** target → Signing & Capabilities → check
"Automatically manage signing" → pick your team (your Apple ID) → plug in
the iPhone → press ▶. First run on a free Apple ID: on the phone go to
Settings → General → VPN & Device Management → trust your developer cert.

## After web-code changes

The native shell serves the same `index.html`. After editing it:

```bash
npx cap sync ios   # copies web assets into the iOS project
```

and rebuild in Xcode. (Or point Capacitor at the live GitHub Pages URL with
`server.url` in capacitor.config.json for instant updates — dev convenience,
not recommended for production.)

## What the native path changes

- Decode: ML Kit on-device (Neural Engine) — dramatically better on small,
  angled, glossy, and damaged labels than any web engine
- Camera: rendered natively *behind* the webview (the app's CSS goes
  transparent in scan mode) — zero getUserMedia overhead
- Everything else (scan-once, OH YEAH, xlsx, Teams, → Michael) is identical:
  same HTML/JS
