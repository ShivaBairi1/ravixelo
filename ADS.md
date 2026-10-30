# Making money with Ravixelo: ads setup

## What is built
| Where | Provider | What shows | Code |
|---|---|---|---|
| Android app | **Google AdMob** | Banner on menu/result screens; interstitial when leaving a result screen (max every 2nd run, 2+ min apart, never in the first 2 runs); **rewarded** "continue after defeat" and "+payout bonus after a win" | `src/ads.ts`, `src/adPolicy.ts`, `android/.../AdsPlugin.java` |
| Website | **Google AdSense** | Banner on menu/result; H5 rewarded `adBreak` once Google approves it for your site | `src/Ad.tsx` |
| Consent | Google UMP form (EEA/UK) + "Ad privacy options" button | automatic | `AdsPlugin.java` |
No ads ever appear during play. Rewarded ads always need a tap. **AdSense is never loaded inside the Android app** (Google forbids it); the app uses AdMob.

## Setup (about 30 minutes, plus Google's review time)
1. Create an AdMob account (admob.google.com). Add an Android app (`com.ravixelo.game`; if it is not on Play yet choose "No"). Copy the **App ID** (`ca-app-pub-1234567890123456~1234567890`).
2. Create 3 ad units: Banner, Interstitial, Rewarded. Copy their IDs (`ca-app-pub-1234567890123456/1234567890`).
3. AdMob > Privacy & messaging: create the **GDPR consent message** (European regulations) and publish it, or EEA users will get no consent form.
4. GitHub repo > Settings > Secrets and variables > Actions. Secrets: `ADMOB_APP_ID`, `ADMOB_PUBLISHER_ID` (`pub-1234567890123456`), `ADMOB_BANNER_ID`, `ADMOB_INTERSTITIAL_ID`, `ADMOB_REWARDED_ID`. Variable: `PRIVACY_URL`.
5. Test: Actions > Android build > Run workflow with *test ads* ON. Install the debug APK. You should see Google's "Test Ad" labels. **Never click your own live ads** (account ban risk); use test ads or add your phone as a test device.
6. Release: tag `v1.0.0`. The tagged build uses your real IDs. Host `app-ads.txt` (produced by the build, also uploaded as an artifact) at the root of the website listed on your Play page, then in AdMob > Apps > app-ads.txt check it.

## Adding third-party networks (more fill, more revenue)
Best path: **AdMob mediation** (AdMob console > Mediation). Add networks (e.g. AppLovin, Unity Ads, Meta, ironSource) there; then add each network's Gradle adapter dependency in `android/app/build.gradle` (the console shows the exact line) and, if the network requires it, its app ID in the manifest. No JS change needed: `ads.ts` keeps calling the same three ad units. Alternative: swap `AdsPlugin.java` for another SDK; the JS interface in `ads.ts` (`NativeAds`) is the only contract.

## Tuning
- Frequency: `PACE` in `src/adPolicy.ts` (grace runs, minimum gap, every N runs).
- Rewarded hooks: continue (`watchContinue`) and bonus (`watchDouble`) in `src/heist/heistMain.tsx`. More rewarded ideas: ad for a free upgrade level, ad for extra ammo at start.
- Retention drives revenue: upgrades, difficulty tiers and the daily loop keep players coming back.

## Rules to respect (policy summary, verify current text)
Do not place ads where they cause accidental taps; do not auto-click or incentivise clicks; label rewarded ads clearly (the buttons say "Watch ad"); declare ads and the advertising ID in Play Console; keep the privacy policy URL working; the game is not for children, so do not enable child-directed settings.
