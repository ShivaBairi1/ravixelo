# Independent review of Ravixelo (v0.6)

Basis: reading the code, the design and the automated tests. NOT a play session: nobody has run this game in a real browser or on a phone yet. Scores are therefore estimates, not measurements.

## Rating
- Before this review's fixes: **5 / 10**. Ambitious feature set, but unverified feel, no onboarding, silent, one rare "hook".
- After the fixes below, projected: **6.5 / 10**, still unproven until real players test it. A rating above 7 needs playtest data (see "How to prove it").

## Problems found, and what was done
| # | Problem | Status |
|---|---|---|
| 1 | Never playtested; tuning (speeds, chase, bomb range) is guesswork | NOT fixed: needs real play. See "How to prove it" |
| 2 | No onboarding: a new player would not know swipe/punch/bomb | FIXED: contextual hints during the first 3 runs |
| 3 | Few memorable "reasons to come back" (no goals beyond high score) | FIXED: 3 daily missions, +2 keys each |
| 4 | Rare/unclear core hook; robbery was a pure penalty | FIXED: "Bank it or lose it" (carry cash, robbers steal half of carried cash only, blue vaults bank it) |
| 5 | Silent apart from beeps; weak game feel | FIXED: procedural music that speeds up per level, camera shake, phone vibration |
| 6 | ~300 draw calls and 5 MB bundle may hurt low-end Android | PARTLY fixed: auto low-quality mode (fewer buildings, lower pixel ratio) on weak devices. Bundle size not reduced |
| 7 | Hold-to-steer plus swipes may conflict on touch; no lane mode | NOT fixed: needs playtest, then decide on an optional lane-snap mode |
| 8 | No automated tests for the 3D engine (needs a browser) | NOT fixed: add Playwright tests in CI |
| 9 | Duels: unverified network play, cheating possible, no TURN relay | NOT fixed: needs a server |
| 10 | Purchased keys cannot be verified without a backend | NOT fixed: do not sell keys yet |
| 11 | Character is original but stylized/procedural, not a studio model | NOT fixed: needs an artist or a licensed asset |
| 12 | Android app: AdSense is not allowed in apps | NOTED: use AdMob (not added) |

## Uniqueness: honest analysis
A "90% different from other games" figure cannot be measured, and claiming it would be misleading. The game sits in the well-known endless-runner genre (auto-run, coins, power-ups, chaser, revive, daily missions). That structure is shared with many games.

What is less common in combination:
- **Bank it or lose it**: your carried cash attracts loss, vaults make you plan routes. A simple risk/reward hook.
- **Full action set against varied threats** in one runner: jump, slide, punch, bomb, each with a matching obstacle (hurdle, overpass, robber, traffic jam).
- **Districts** that change the whole scenery every few km.
- **1v1 same-seed duel with built-in voice chat**, joined by a link.

Verdict: a distinctive *combination* inside a familiar genre, not a new genre. People are most likely to love: the bank-or-lose-it tension, bombs clearing a jam, quick duels with a friend. Risks: it may feel busy, controls may feel loose, and new players may not discover the vault idea quickly.

## How to prove it (what to do next)
1. Get 10 real players on phones for 3 sessions each. Record: day-1 return rate, average session length, the first obstacle that kills new players, and whether they find the vault.
2. Fix what kills players early; adjust chase/bomb/robber numbers (all in `config.ts` and `game.ts`).
3. Only then publish widely and re-rate.

# Follow-up review (v0.8)
Basis: code reading plus the checks listed in README (no browser/phone/Gradle run).

| # | Problem found | Fix |
|---|---|---|
| 1 | Touch buttons were a component declared inside `App`, so React re-created every button ~12x/s (HUD updates): presses could drop and FIRE could stick | `Btn` moved outside `App`, pointer capture + lost-capture release |
| 2 | "Silent run" bonus and 3rd star were unreachable: drilling always forced the alarm, and any hit on a guard (even suppressed) raised it | Drill is loud but no longer forces the alarm; suppressed pistol silently kills unaware guards |
| 3 | Pausing left the alarm siren looping; backgrounding the app did not pause | Pause silences/suspends audio; auto-pause on visibility/pagehide |
| 4 | Retry after game over built the level twice; bullet holes, tracers, particles survived a restart | Single build, `resetFx()`, new `toMenu()` |
| 5 | Keys 1 and 2 both toggled weapon (key 1 could switch to the pistol) | `press('rifle'/'pistol')` |
| 6 | Hit marker existed in code but never reached the screen; its timer logic was broken | Proper timer, HUD marker |
| 7 | Android: portrait lock made the touch layout overlap; back button exited the app; mic permission requested for a feature the default mode lacks; fixed versionCode (Play rejects re-uploads); AdSense could load inside the app (policy violation); no ads in the default mode at all | Landscape + immersive, back handler, mic removed (runner/duels web-only), versionCode from CI, AdSense gated off natively, AdMob plugin + UMP consent |
| 8 | Runner: spent revive keys even when the revive failed | Charge only on success |
| 9 | Page title/meta/store text still described the old runner; privacy page lacked ads/Android | Updated |
Still open: real playtests (balance, touch aiming feel, performance on low-end phones), no aim assist on touch, no server (leaderboards, duel cheating, purchases), art is procedural.
