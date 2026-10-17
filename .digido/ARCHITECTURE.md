# ARCHITECTURE - kids-game-1

Mapped 2026-10-17. Two static browser games for the phone, hosted on GitHub Pages.
No build step, no backend, no accounts. Everything a player has lives in their own browser (localStorage).

| Game | Path | URL | Size |
|------|------|-----|------|
| Kart Rush (3D, English) | `index.html` | https://digimate-rani.github.io/kids-game-1/ | 1142 lines |
| Road Race (2D, Hebrew, classic) | `classic/index.html` | https://digimate-rani.github.io/kids-game-1/classic/ | 1289 lines |

Git: `main` deploys straight to Pages (Settings -> Pages -> main / root). Tag `v1-road-race` = the 2D game before the move to `classic/`.

---

## 1. File layout

```
kids-game-1/
  index.html        Kart Rush: HTML + CSS + one <script type="module"> (all game code)
  manifest.json     "Add to home screen" data for Kart Rush (fullscreen, landscape)
  icon.svg          Kart Rush icon
  README.md         Player-facing description of both games
  classic/
    index.html      Road Race: one canvas 2D game, no libraries
    manifest.json   Road Race home-screen data (portrait)
    icon.svg
  .digido/          This map, STACK.md, STATE.md
```

---

## 2. Kart Rush - code map (`index.html`)

One file, three layers:

1. **CSS (lines ~19-200)** - HUD chips, steering wheel, pedals, menu, finish and pause screens. Media queries for short landscape screens (`max-height: 430px`) and portrait.
2. **DOM (lines ~200-285)** - `#gl` canvas (3D), `#hud`, `#controls` (wheel SVG + GAS/BRAKE), `#menu`, `#finish`, `#pause`, `#muteBtn`.
3. **Module script (line ~286 to end)** - the game. Sections in order:

| Section | Line | What it does |
|---------|------|--------------|
| save | 289 | `localStorage['kart-rush.v1']` = `{ best, bestLap, medal, color, track, mute }` per track id |
| data | 295 | `LAPS=3`, `W2=7` (half track width, m), `VMAX=27`, `VBOOST=36` (m/s), 8 kart `COLORS`, `AI_NAMES`, 3 `TRACKS` (control points, sky, fog, ground, scenery type) |
| renderer | 322 | `WebGLRenderer` (antialias, pixel ratio up to 2, PCF soft shadows), camera, hemisphere light, sun with a 1024 shadow map that follows the player |
| textures | 348 | All textures are drawn on small canvases at load time (asphalt, kerb, wall, checker, boost arrows, banner, sky gradient, ground). No image files |
| materials / geometries | 429 | Shared kart parts (`KG`) and materials (`M`) |
| `makeKart()` | 456 | Builds one kart from ~20 primitive meshes (chassis, body, pods, wing, driver, helmet, 4 wheels on pivots) |
| `buildWorld(def)` | 514 | Builds a whole track: Catmull-Rom loop through the control points (x1.35), resampled every ~1 m into arrays `P` (points), `T` (tangents), `R` (right vectors), `K` (curvature over 8 m). Then ribbons for shoulder, asphalt, kerbs, walls; tire stacks on sharp corners; start gate with 3 start lights; grandstand with an instanced crowd; 3 boost pads on the straightest parts; instanced scenery (trees / cacti + rocks / pines + snowmen); mountains or mesas; clouds; sky + fog |
| karts | 711 | 6 kart objects created once and reused. `placeGrid()` puts them on a 2x3 grid behind the line (player starts 4th) and gives each AI a `skill` |
| `syncMesh()` | 742 | Copies physics state to the 3D model (position, heading, lean, front-wheel steer, wheel spin) |
| input | 753 | Pedals = pointer events per element (multi-touch). Wheel = relative horizontal drag, 62 px = full lock, springs back on release. Keyboard: arrows / WASD, Esc pauses |
| sound | 783 | Web Audio only: engine (two oscillators through a low-pass), beeps, bump noise, finish fanfare. Vibration on Android |
| race logic | 828 | State machine `menu -> countdown -> race -> finished`, plus `paused` |
| `updateTrackPos()` | 833 | Finds the nearest track sample (searches 30 either side of the last one). Counts laps when the index wraps from the last 20% to the first 20%, only after passing the halfway point (no cheating by reversing) |
| `drive()` | 860 | The one physics function for player and AI: acceleration, braking / reverse, coasting drag, grass slowdown, boost, steering, walls, boost pads |
| `aiControl()` | 902 | AI driver: aims at a point ahead on the track with its own lane wobble, slows for curvature ahead, rubber-banding against the player |
| `collide()` | 922 | Kart-to-kart push apart, small speed loss |
| HUD | 935 | Position, lap, time, speed, minimap (2D canvas), toasts, WRONG WAY, wheel rotation |
| flow | 977 | `startRace`, `playerFinished`, `showResults` (estimates times for karts still racing), pause, menu |
| menu | 1033 | Track cards with a drawn track shape and best time / medal; color swatches |
| loop | 1070 | `requestAnimationFrame` loop: menu orbit camera, countdown lights, race step, chase camera, render |
| test hook | 1139 | `window.__k` (start, skip countdown, jump to finish, karts, input) for automated screenshots |

### 2.1 Tuning knobs (where to change the feel)

| Feel | Where | Current value |
|------|-------|---------------|
| Top speed | `VMAX`, `VBOOST` (line 298) | 27 / 36 m/s (97 / 130 km/h on the HUD) |
| Acceleration / braking | `drive()` | +13 m/s² falling off near top speed, brake -28, coast -4.5 |
| Steering | `drive()` line 877 | `2.15 rad/s`, reduced up to 36% at top speed |
| Grass slowdown | `drive()` | top speed x 0.52 |
| Wall bounce | `drive()` | limit `W2 + 3.4` from center, speed x 0.6 |
| AI skill | `placeGrid()` | `[1.0, 0.985, 0.97, 0.955, 0.99]` x `DIFF[save.diff].s` (Easy 0.88 / Normal 0.94 / Hard 1.0) x VMAX |
| AI corner speed | `aiControl()` | from the steering physics: `0.96 * TURN0 / (curvature + TURN0 * TURNF / 30)`; same formula the player is bound by |
| AI line / mistakes | `aiControl()` | aims at the inside of the coming corner; lifts the gas for `DIFF.err` seconds every 4-13 s |
| Rubber band | `aiControl()` | up to `+DIFF.cu` (8 / 12 / 20%) when the player leads by 50+ m, -16% when the AI leads by 30+ m |
| Tire scrub | `drive()` | `-7 * steer^2` m/s² above 10 m/s, so full lock at speed costs ~15% |
| Frame cap / quality | `frame()`, `applyQuality()` | 60 fps race, 30 menu, no render when paused or on results; dpr 1.5 (eco 1.0), eco = no shadow map + blob shadows |
| Laps | `LAPS` | 3 |
| Track width | `W2` | 14 m |

### 2.2 How a frame runs
`frame()` -> (race) `drive(player)` with wheel + pedals -> `aiControl()` x5 (each calls `drive()`) -> `collide()` -> `syncMesh()` x6 -> `updateHud()` (DOM text + minimap redraw) -> chase camera -> move sun shadow to the player -> `renderer.render()`.
Nothing is capped: the loop runs at the screen's refresh rate (60, 90 or 120 per second).

---

## 3. Road Race (classic 2D) - short map (`classic/index.html`)

Canvas 2D, Hebrew RTL text, logical width 360 scaled to the screen.
- Sprites (cars, trees, coins) are painted once at high resolution and reused.
- Endless road: curve function `curve(d)`, traffic in rows that always leave a free lane, coins, turbo bolt.
- 4 worlds every 600 m: meadow, desert, snow, night (night uses a half-resolution light mask for headlights and street lamps).
- Garage with 6 cars bought with coins. Save key `kids-game-1.v1`.
- Test hook `window.__g` (`set(screen)`, `warp(meters)`).

---

## 4. How it is tested
- Headless Chromium through Playwright (`ronit-agent/tools/playwright/node_modules`), phone sizes 844x390 and 390x844, WebGL through SwiftShader (software, no GPU).
- Checks: no console errors, screenshots of menu, countdown, race on all 3 tracks, results.
- Limit: headless has no GPU, so it shows 4-6 frames per second. Real-phone speed, heat and battery are **not** measured by these tests.
- Local server for module loading: `venv python -m http.server 8765 --directory <PROJECTS_ROOT>/kids-game-1`.

---

## 5. Feedback (Rani, 2026-10-17) - RESOLVED in session 2 the same evening

Both items below were fixed (see the tuning table in 2.1 and STATE.md). The analysis is kept as the record of why.
Still to measure: real-phone fps / heat with and without Battery saver (`?debug` overlay).

Liked: steering wheel and pedals feel good, "really fun".

### 5.1 The player is much faster than the AI when driving clean
**Symptom:** staying off the grass and away from the walls, the player pulls far ahead.

**Why it happens (from the code):**
1. **AI top speed is lower by design.** Each AI drives at `VMAX x skill` (0.88-0.97), so on every straight it is 1-3 m/s slower than the player.
2. **AI brakes for corners much more than it needs to.** `aiControl()` cuts speed by up to 45% when curvature > 0.12. The player's steering (`2.15 rad/s`, ~1.4 rad/s at top speed) can take most corners on these wide (x1.35) tracks at nearly full speed, so the player never has to brake and the AI always does.
3. **AI wastes distance.** Its lane wobbles ±1.6 m around a fixed lane, so it never takes the inside line of a corner.
4. **Rubber-banding is too weak.** At most +10%, and the corner caution still caps that.

**Fix directions (to decide):**
- Calculate the AI corner speed from the real physics (the speed at which the turn rate can follow the curve) instead of a fixed 45% cut.
- AI racing line: aim for the inside of corners instead of a fixed lane.
- Stronger catch-up when the player leads (for example up to +20%, only when out of sight).
- A difficulty choice in the menu (Easy / Normal / Hard), which mostly changes AI skill and catch-up.
- Optional: make the player lose a little grip at top speed so tight corners need a lift of the gas.

### 5.2 The phone heats up (probably uses a lot of battery)
**Likely causes, biggest first (from the code):**
1. **No frame cap.** Phones with 90/120 Hz screens render 90-120 frames per second. A kids' racing game is fine at 60, or 30 in the menu.
2. **High resolution.** Pixel ratio up to 2 plus antialiasing = up to 4x the pixels of the screen size, every frame.
3. **Real-time shadows.** A 1024 soft shadow map redrawn every frame, with ~120 kart parts casting shadows.
4. **Blur over 3D.** `backdrop-filter: blur(6px)` on every HUD chip (CSS line 40) forces the browser to blur the moving 3D picture behind 5 boxes every frame.
5. **HUD redraws every frame.** Minimap canvas and DOM text updated 60-120 times a second (`updateHud`, line 960).
6. **Many draw calls.** Each kart is ~20 separate meshes (x6 karts).
7. The 3D scene keeps rendering behind the menu, pause and results screens.

**Fix directions (to decide):**
- Cap to 60 fps in races, 30 fps in the menu, stop rendering when paused or on the results screen.
- Pixel ratio 1.25-1.5; turn off antialiasing on high-density screens.
- Cheaper shadows: one soft blob under each kart instead of real-time shadow maps (or update the shadow map every other frame).
- Remove `backdrop-filter` during the race (solid semi-transparent chips look the same).
- Update minimap and text 10-15 times a second.
- Merge each kart's parts into 2-3 meshes.
- A "Battery saver" switch in the menu that applies the cheap settings.
- Add a small debug overlay (`?debug`) that shows real frame time on the phone, to measure before and after.
