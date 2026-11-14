# STATE - kids-game-1

last_updated: 2026-11-14
last_action: Road Race v2 (2D) built - phases A-E, Gemini 4 Hydrogen builder, Claude supervisor (session 3)
completed_skills_this_session: [codebase-mapper, execute]

## Where things stand
- Kart Rush (3D) live at the root URL, Road Race (2D) at /classic/.
- Session 2 (2026-10-17 evening) closed both open feedback items from ARCHITECTURE.md section 5:
  - 5.1 balance: AI corner speed from the real steering physics, inside racing line, catch-up from 50 m behind,
    random small mistakes, tire scrub for everyone, Easy / Normal / Hard in the menu (`DIFF` table).
  - 5.2 battery: 60 fps cap in race / 30 in menu, no rendering behind pause and results, pixel ratio 1.5 (1.0 in
    Battery saver), antialias only on low-density screens, PCF 768 shadow map, no backdrop blur, HUD and minimap at
    12 Hz, Battery saver switch (no shadow map, blob shadows), `?debug` overlay with fps / render ms / draw calls.
  - Extras: lap time on the lap toast, "X passed you!" / "P2!" toasts with sounds.
- Verified headless: no console errors, rendering frozen while paused, balance simulation (`window.__k.sim(sec)`,
  player as a perfect AI): Easy wins by ~7 s, Normal about even to +2 s depending on start traffic, Hard within ~1 s.
- Not measured: real-phone fps, heat, battery. Rani checks with `?debug` on his phone.

## Session 3 (2026-11-14) - Road Race v2
- `classic/index.html` grew 1289 -> 3482 lines: Journey (20 stages, stars, bosses), rival AI, road hazards per world,
  goal tracker, pause button, upgrades, 13 cars. Spec `.digido/ROAD-RACE-V2-DESIGN.md`, roadmap
  `.digido/ROAD-RACE-V2-ROADMAP.md` (phases A, B, B2, C, C2, D, E all done).
- Built by Gemini 4 Hydrogen high via agy (agent-bridge thread `agent-bridge/2026-11-14-road-race-v2`, gitignored),
  reviewed by Claude with headless Playwright + `__g.simRace` balance runs.
- Final balance (zero-mistake bot, no upgrades): all stages survivable; duels 10/10 meadow, 10/10 desert, 10/10 snow,
  8/10 night; bosses meadow 12/12, desert 11/12, snow 4/4, night 3/4. A kid is weaker than the bot.
- Not measured: real phone fps / heat for v2, real kid play.

## Next
Road Race v2: Rani's kid plays it; tune `TUNE` / `STAGES` from what feels too hard or too easy.
Kart Rush: Rani plays on the phone: (1) does Normal feel like a fight, (2) heat with and without Battery saver, (3) `?debug` fps.
Then tune `DIFF` / quality defaults from his numbers.
