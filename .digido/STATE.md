# STATE - kids-game-1

last_updated: 2026-10-17
last_action: Balance + battery upgrade of Kart Rush (session 2)
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

## Next
Rani plays on the phone: (1) does Normal feel like a fight, (2) heat with and without Battery saver, (3) `?debug` fps.
Then tune `DIFF` / quality defaults from his numbers.
