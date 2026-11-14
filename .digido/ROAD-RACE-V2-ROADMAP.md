# Road Race v2 - ROADMAP (builder: Gemini, supervisor: Claude)

Spec: `.digido/ROAD-RACE-V2-DESIGN.md` (APPROVED 2026-11-14). Code map of the current game: `.digido/ARCHITECTURE.md`
section 3. Target file: `classic/index.html` (one file, canvas 2D, Hebrew RTL, no libraries, no build step).

## How we work
- The builder edits ONLY the working copy `agent-bridge/<thread>/files/build/index.html` (a copy of
  `classic/index.html`). Never touches `classic/`, `index.html` (the 3D game) or anything else. The supervisor
  reviews, tests in a headless browser, and copies the approved file into `classic/`, then commits and pushes.
- One phase per turn. The builder ends every phase with a `done` message: what changed, where in the file
  (function names), how to test it (hook calls), known gaps. That message is the phase SUMMARY (Digido).
- Speed matters: no questions unless blocked; pick sensible values and list them in the summary.

## Rules for every phase (hard)
1. Keep one file, plain JS, no external libraries, no network calls. Keep `'use strict'` IIFE style.
2. Keep the existing save: key `kids-game-1.v1`, existing fields (`best`, `coins`, `owned`, `car`, `mute`) keep
   their meaning. Add new fields with defaults (old saves must load without errors).
3. Free ride (the current endless game) keeps working exactly as today, plus the speedometer.
4. All on-screen text Hebrew, short (player age 8+). No em-dash character anywhere (code comments too).
5. Phone first: logical width 360, works in portrait 390x844 and must not break at 844x390. Touch targets >= 44 px.
6. Performance: the phone heated up in the 3D game. No per-frame allocations in hot loops where avoidable, reuse
   sprites (`sprite()` cache), no shadowBlur in per-frame drawing of many objects, HUD text cheap.
7. Extend the test hook `window.__g` for every new feature (see each phase). It is how the supervisor tests.
8. No console errors. No `alert`, no `prompt`.

## Phase A - Journey framework + speedometer
- Main menu: big "מסע" button, "נסיעה חופשית", "מוסך". Current best + coins stay visible.
- Journey map screen: 4 worlds x 5 stages, locked / open / stars per stage, boss tile with crown, world locked
  until previous boss beaten, boss locked until 8 of 12 stars of stages 1-4 (show "צריך 8 כוכבים").
- Stage table `STAGES` (20 rows) with: world, type (`finish` | `race` | `duel` | `boss`), length (m), speed factor,
  traffic density, 3 goals (`{kind, n, label}` kinds: finish, coins, nocrash, smash, time, lead), star coin rewards.
  In phase A types `race` / `duel` / `boss` play as `finish` runs (rival comes in phase C) but keep the type.
- A stage runs in its world's biome only (no biome change mid stage). Finish line drawn on the road, progress bar
  on top (player dot to flag). Crossing it = stage end. Lose all hearts = failed.
- Stage start card (name, 3 goals) and stage end card (stars earned animated, coins, "שוב" / "הבא" / "מפה").
- Save: `stars` per stage (best kept), first-time star coin bonus paid once per star.
- Speedometer on the HUD in Journey and Free ride: km/h = speed x factor (normal ~60-140, turbo higher), arc gauge
  or bar plus number, turbo colour. Drawn cheaply.
- UI polish is welcome here: this is the screen the kid sees most. Same style family as the current game.
- Hooks: `__g.stage(w, s)` starts world w (0-3) stage s (0-4); `__g.finish()` forces the finish line now;
  `__g.save` returns the save object; `__g.unlockAll()`; `__g.set('map')`.
- Done when: all 20 stages playable start to finish, stars and unlocks persist across reload, Free ride unchanged
  plus speedometer, no console errors.

## Phase B - Harder roads
- One mechanic per world, introduced alone in stage 1 of that world, combined later, scaled by the stage table:
  meadow lane-changing cars (blinker ~0.8 s before moving), desert narrow 3-lane sections (signposted) + sand
  patches + cone road works, snow ice patches (steering lag / slide) + sharper curves, night oncoming lane (arrows on
  the road) + potholes. Rule from the current game kept: there is always a free path.
- Hooks: `__g.mech()` lists active mechanics; `__g.spawn(kind)` places one ahead of the player.
- Done when: each mechanic visible and fair on screenshots, no impossible rows (always a way through).

## Phase C - Rival
- Per spec section 2: own D and lane, lookahead lane choice (traffic free + coins), reaction delay, mistakes, mild
  rubber band +-6%, steals coins (popup "גנב לך מטבע!"), uses turbo, contact = both bounce no heart loss, player
  turbo = rival spin ~1.5 s. On-screen draw, edge arrow + distance when off screen, two dots on the progress bar.
- Characters: בני הבזק, סופה, קרחון, צל (simple drawn face icon + colour each), taunt line at start / end.
  Boss = same character, stronger factor, hardest road of its world.
- Stage types now real: `race` first to the line, `duel` more coins at the line, `boss` race.
- Hooks: `__g.rival` (D, lane, coins, state); `__g.simRace(sec)` runs the race headless with the player driven by
  a simple bot and returns `{playerD, rivalD, playerCoins, rivalCoins}` so balance can be measured.
- Done when: rival races look natural on screenshots, a decent bot beats world 1 rivals but not trivially, boss is
  clearly harder.

## Phase D - Upgrades and premium cars
- Garage tabs: מכוניות / שדרוגים. Upgrades and prices per spec section 4 (extra heart, long turbo, magnet pickup,
  shield pickup). New pickups on the road (magnet pulls coins, shield absorbs one hit, visible ring).
- Premium cars ברק 600 (+10% top speed), מגנטית 900 (small built-in magnet), טנק 1200 (first hit per stage free),
  drawn with the existing vehicle painter. Existing 6 cars stay cosmetic.
- Hooks: `__g.coins(n)` sets coins; `__g.buy(id)`.
- Done when: every upgrade changes play as described, prices and save persist, old save loads.

## Phase E - Polish pass
- Builder's own UI pass across all screens (transitions, juice, sounds), then supervisor checklist fixes.
