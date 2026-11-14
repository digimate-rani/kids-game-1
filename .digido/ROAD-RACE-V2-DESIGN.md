# Road Race v2 - design (classic 2D game)

Status: APPROVED by Rani 2026-11-14 (all proposals as written, plus the speedometer below). Track: Quick build (extended), no ROADMAP / PLAN files.
Hebrew review page: `.digido/design-v2/index.html`.

## Problem
`classic/index.html` is an endless runner: 3 hearts, score = distance/50 + coins x10 + smashes x25, the only goal is a
new best. All 6 cars cost 590 coins in total and are cosmetic. Once the gold car is bought, nothing is left to chase.

## Decisions taken with Rani
- All four layers: Journey (stages + stars), computer rival, harder roads, upgrades for coins.
- Player age 8+, must stay a real challenge.
- Rival = fair competitor (no item throwing, no blocking tricks).
- Endless mode stays as "Free ride". Existing save (coins, cars, best) carries over.

## 1. Journey
- Menu: Journey (main button), Free ride, Garage.
- Map: 4 worlds (the existing biomes: meadow, desert, snow, night) x 5 stages = 20 stages.
- Every stage has a finish line (finite length) and 3 goals = up to 3 stars:
  star 1 = finish (or win), stars 2 and 3 = stage goals (collect N coins, no crash, smash N cars, finish under T, beat
  rival by X m).
- Stage types: Finish run, Rival race (first to the line), Coin duel (more coins than the rival by the line),
  Boss race (world's strongest rival on that world's hardest road).
- Unlock: next stage opens at 1 star. The boss (stage 5) needs 8 of the 12 stars of stages 1-4. Next world opens when
  the boss is beaten. Forces replays for stars = the challenge for an older kid.
- Coins: first-time star bonus (e.g. 20 / 40 / 60 per star, growing per world) plus coins collected on the road.
- Hearts: 3 (4-5 with upgrades). Losing all hearts = stage failed, retry.

## 2. Rival
- One rival car per race stage, own position D and lane; same base speed as the player x stage factor (0.95-1.08).
- Driving AI: looks ahead ~2 rows, picks the lane that is free of traffic and has coins, with a reaction delay; can
  misjudge and bump traffic (slows 1 s, no hearts). Mild rubber band (+-6%) keeps the race close but beatable.
- Takes coins it drives through (popup "stole a coin"). Uses turbo bolts too.
- Contact with the player: both bounce sideways, nobody loses a heart. Player with turbo: rival spins out ~1.5 s.
- Display: drawn on the road when on screen; arrow + distance at top/bottom edge when off screen; progress bar at
  top with flag, player dot and rival dot.
- Characters (one per world, the boss is the same character at full strength): בני הבזק (meadow), סופה (desert),
  קרחון (snow), צל (night). Face icon + short taunt line at stage start and end.

## 3. Harder roads - one new mechanic per world, introduced in stage 1, combined later
- Meadow: lane-changing cars (blinker flashes ~0.8 s before the move = fair warning).
- Desert: narrow road (3 lanes for a section, signposted), sand patches (slow like grass), road works with cones.
- Snow: ice patches (steering slides, delayed grip), sharper curves.
- Night: oncoming lane (cars coming toward you in one lane, marked with arrows), potholes (lose speed).
- Base speed and traffic density rise per world and per stage (stage table drives all of it).

## 4. Upgrades and cars
- Garage gets two tabs: Cars, Upgrades.
- Upgrades (escalating prices, all above the old 590 total):
  - Extra heart: 4 hearts (300), 5 hearts (800).
  - Long turbo: 3.2 s -> 4.0 -> 4.8 -> 5.6 s (100 / 200 / 400).
  - Magnet (new pickup on the road, pulls coins): unlock 150, duration 5 -> 7 -> 9 s (150 / 300).
  - Shield (new pickup, absorbs one hit): unlock 250, appears more often at level 2 (400).
- New premium cars with one ability each (open decision: also give the existing 6 small abilities?):
  e.g. "ברק" 600 (+10% top speed), "מגנטית" 900 (built-in small magnet), "טנק" 1200 (first hit each stage free).

## Build order (each step playable and pushed)
A. Journey framework: menu, map, stage table, finish line, goals, stars, unlocks, star coins, save v2 fields.
B. Harder road mechanics per world.
C. Rival (AI, stealing, contact, display, characters, boss).
D. Upgrades + new pickups + premium cars, price tuning.
Verification per step: Playwright screenshots at 390x844, no console errors, `window.__g` hooks for jumping to a
stage, simulation of a rival race to check it is winnable but not trivial.

## 5. Speedometer (added by Rani at approval)
- A speed display on the HUD for the player's car, in every mode (Journey and Free ride).
- Shows km/h derived from the internal `speed` (game units per second) with a fixed factor so normal driving reads
  roughly 60-140 km/h and turbo visibly jumps higher. Animated needle or bar plus the number; turbo colours it.

## Approved answers (2026-11-14)
1. Boss needs 8 of 12 stars. 2. Rival contact never costs a heart. 3. Existing 6 cars stay cosmetic; only the 3 new
premium cars have abilities. 4. 20 stages. 5. Rival names approved.
