# STACK - kids-game-1

Mapped 2026-10-17.

| Layer | What | Version / source |
|-------|------|------------------|
| Language | Plain HTML, CSS, JavaScript (ES modules in Kart Rush) | no build, no bundler, no package.json |
| 3D engine | Three.js core only | `0.160.0`, loaded from `https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js` through an import map |
| 2D engine | Browser Canvas 2D (classic game) | built in |
| Fonts | Lilita One, Nunito | Google Fonts |
| Sound | Web Audio API (generated tones, no sound files) | built in |
| Storage | `localStorage` keys `kart-rush.v1`, `kids-game-1.v1` | per device |
| Hosting | GitHub Pages, branch `main`, root folder | repo `digimate-rani/kids-game-1` (public) |
| Install to phone | Web app manifest + SVG icon ("Add to home screen") | `manifest.json` in each game folder |
| Testing | Playwright + headless Chromium (SwiftShader WebGL) | from `<PROJECTS_ROOT>/ronit-agent/tools/playwright` |

External runtime dependencies: jsDelivr (Three.js) and Google Fonts. Without internet the 3D game does not start (no offline cache / service worker).
