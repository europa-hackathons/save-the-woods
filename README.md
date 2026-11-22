# Save the Woods! 🐺🌲

**Save the Woods!** is a browser-based, single-file arcade game where you play as a wolf hero defending 5 forest trees from chopper robots (represented as data centers) and 3 scheduled bosses within a 90-second round. Designed to be played with **one hand via a 4-box webcam motion grid**, it also includes full keyboard backup controls.

**Built by:** Ezra, Milian, and Yoonchae under supervision from Manish

---

## Run it

The game has no build step or external dependencies.

### Open in browser
Double-click `index.html` to open it directly in any modern browser. If camera access is unavailable or denied, the game automatically falls back to keyboard controls.

---

## Files

- `index.html` — The complete, self-contained game (HTML layout, CSS styling, webcam motion detector, Web Audio sound effects, game logic, and Canvas 2D renderer).
- `README.md` — Project documentation.

---

## Controls

### Webcam (4-Box Motion Grid)
Once you click **▶ Play Game**, a mirrored webcam preview appears in the bottom-right corner divided into a 2×2 grid. Wave one hand inside a box to move:

| Top Row (Jump) | |
|---|---|
| **Box 1 (Top-Left):** ↖ Jump Left | **Box 2 (Top-Right):** ↗ Jump Right |

| Bottom Row (Walk) | |
|---|---|
| **Box 3 (Bottom-Left):** ⬅ Walk Left | **Box 4 (Bottom-Right):** ➡ Walk Right |

Use the **Touch Sens** slider beneath the preview (`8`–`60`, default `22`) to adjust motion sensitivity for your lighting and camera.

### Keyboard Backup
- **`1`, `2`, `3`, `4`** — Trigger boxes 1–4 directly (same as waving in the corresponding webcam box).
- **`←` / `A`** and **`→` / `D`** — Walk left / walk right.
- **`Q`** or **`←`/`A` + `↑`/`Space`** — Jump left.
- **`E`** or **`→`/`D` + `↑`/`Space`** — Jump right.

### Gameplay Actions
- **Defeat Chopper Robots (🤖):** Stand next to a robot and keep waving (or holding any movement/box key) to "rub" it until its progress bar fills and it pops.
- **Defeat Bosses:** Watch the banner above the boss's head and touch the **2 box numbers** in order. The next required box is highlighted in blue on the webcam preview.

---

## How the game is organised

Everything lives in a single file (`index.html`), structured into three sections:

1. **Styling (`<style>`):** Responsive dark-forest layout with a fixed `1200/760` game container, translucent top HUD bar, bottom-right webcam preview widget, and Start / Game Over overlays.
2. **Markup (`<body>`):**
   - `#game` (`1200×760` canvas) for rendering the scene.
   - `#hud` displaying score, remaining trees, defeated bosses, last action, and countdown timer.
   - `#cam-box` containing the `240×180` preview canvas, sensitivity slider, and camera status text.
   - `#start-screen` and `#gameover-screen` overlays, plus a hidden `<video id="webcam">` element.
3. **Script (`<script>`):**
   - **Configuration (`CONFIG`):** Central table for round timing, physics, entity sizes, spawn rates, scoring, motion thresholds, and `bossSchedule`.
   - **Audio (`playTone`):** Lightweight Web Audio API oscillator beeps for jumps, robot pops, boss arrivals, sequence hits, and boss defeats.
   - **Webcam Motion Pipeline (`initWebcam`, `processWebcam`):** Captures a `320×240` video stream, mirrors it onto an offscreen `160×120` canvas (`procCanvas`), compares consecutive frames by sampling every 2nd pixel across the 4 quadrants, and renders the live 4-box overlay.
   - **Game Loop & State (`resetGame`, `handleZoneInput`, `update`, `spawnRobot`, `addParticles`, `endGame`):** Runs via `requestAnimationFrame` (with `dt` clamped to `0.05s`), handling hero physics, robot targeting and tree chopping, rub-to-pop combat, scheduled boss waves, particle physics, HUD updates, win/lose checks, and best-score persistence.
   - **Renderer (`draw`):** Draws the gradient sky, forest floor, trees and chop progress bars, swaying bosses with sequence banners, robots with rub progress bars, the wolf hero, and emoji particles.

---

## Specifications

### Canvas & Video
- **Game canvas:** `1200 × 760` px (`groundY: 650`, horizontal bounds margin: `75` px)
- **Webcam capture:** `320 × 240` px (video only, no audio)
- **Motion processing canvas:** `160 × 120` px (sampled every 2 px)
- **Preview canvas:** `240 × 180` px

### Round & Win/Lose Conditions
- **Round duration:** `90` seconds
- **Starting trees:** `5`
- **Win condition:** Defeat all `3` bosses before time runs out and while at least 1 tree survives.
- **Lose condition:** All `5` trees are chopped down, or the `90s` timer reaches `0`.

### Hero & Combat Tuning
- **Walk speed:** `420` px/s
- **Jump velocity:** `vx = ±440` px/s, `vy = -740` px/s
- **Gravity:** `1650` px/s²
- **Jump cooldown:** `260` ms
- **Rub-to-pop:** Requires staying within `88` px horizontally and `90` px vertically of ground level with any active input for `0.65` s (progress decays at `0.4×` when interrupted).

### Chopper Robots
- **First spawn delay:** `1.2` s (one robot also spawns immediately at round start)
- **Spawn interval:** `4.2` s
- **Max active robots:** `3`
- **Move speed:** `68` px/s
- **Tree chop reach / duration:** `42` px / `3.5` s per tree

### Boss Schedule
Bosses hover at `y = 350` with a horizontal sway of `±140` px and a `320` ms cooldown between sequence touches:

| Time | Boss | Color | Box Sequence |
|---|---|---|---|
| `10s` | **TRIANGLE △** | `#38bdf8` | `2 → 3` |
| `35s` | **SQUARE □** | `#f472b6` | `1 → 4` |
| `60s` | **CIRCLE ○** | `#facc15` | `2 → 4` |

### Scoring & Persistence
- **Robot defeated:** `+1` point
- **Boss defeated:** `+15` points
- **Victory tree bonus:** `+10` points per surviving tree (up to `+50`)
- **End-screen rating:** 1 `⭐` per surviving tree (or `🪵` if none survive)
- **High score storage:** Saved in `localStorage` under `stw_v14_best`

### Motion Detection
- **Pixel difference threshold:** `75` (sum of RGB channel deltas between frames)
- **Zone trigger threshold:** `22` moving pixels per quadrant (adjustable `8`–`60`)

---

## Others

- **Zero dependencies & emoji art:** Built with vanilla HTML5, CSS3, Canvas 2D, Web Audio API, and `navigator.mediaDevices`. All characters and effects use standard emoji (`🐺`, `🌲`, `🪵`, `🤖`, `✨`, `💥`, `🌟`, `💨`).
- **Privacy:** All webcam processing happens locally in your browser; no video or image data is recorded or sent anywhere.
- **Browser resilience:**
  - `localStorage` reads/writes are wrapped in `try/catch` so strict browser privacy modes (such as Safari on `file://`) do not prevent the game-over screen from appearing.
  - `ctx.roundRect` falls back to `ctx.rect` on older browsers.
  - Web Audio calls are wrapped in `try/catch` to avoid errors when autoplay policies block audio.
- **Code note:** A couple of comments in `index.html` still mention "8-Zone" from an earlier iteration, while the active gameplay and UI use the 4-box (2×2) grid.
