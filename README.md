# kids-game-1

Two phone games, plain HTML, no build step.

## Kart Rush (3D) - main page
Play: https://digimate-rani.github.io/kids-game-1/

- Race 5 computer karts over 3 laps.
- 3 tracks: Green Valley, Sunset Canyon, Snow Peak.
- Steering wheel on the left (drag left or right), GAS and BRAKE pedals on the right.
- Blue arrow pads give a speed boost. Grass slows you down.
- Desktop: arrow keys or W A S D.
- Difficulty: Easy / Normal / Hard (computer karts' pace and how hard they chase you).
- Battery saver switch: lower resolution and no real-time shadows, for long sessions or a hot phone.
- Best times and medals are saved on the device.
- Add `?debug` to the URL to see frames per second and render time.

Built with Three.js 0.160 loaded from jsDelivr (`index.html`).

## Road Race (2D, Hebrew) - v2
Play: https://digimate-rani.github.io/kids-game-1/classic/

- Journey: 4 worlds (meadow, desert, snow, night) x 5 stages, 3 goals = 3 stars per stage, boss needs 8 of 12 stars.
- Stage types: finish run, race against a computer rival, coin duel, boss race. Each world has its rival
  (בני הבזק, סופה, קרחון, צל) and its own road hazards (lane-changing cars, sand, narrow road, cones, ice, sharp
  curves, oncoming lane, potholes).
- Live goal tracker and "יעד הושלם!" notice, speedometer, pause button.
- Garage: 13 cars (6 to buy, 4 boss gifts, 3 premium with abilities) and upgrades (extra hearts, long turbo, magnet,
  shield). Free ride = the original endless mode, now with the hazards.
- Steering: drag with a finger, or arrow keys. Save on the device (`kids-game-1.v1`, old saves load).
- Test hooks: `window.__g` (stage, finish, simRace, spawn, pause, buy, ...). Tuning numbers: `TUNE` and `STAGES`.
- Spec and roadmap: `.digido/ROAD-RACE-V2-DESIGN.md`, `.digido/ROAD-RACE-V2-ROADMAP.md`.
- Before v2: git tag `v1-road-race`.
