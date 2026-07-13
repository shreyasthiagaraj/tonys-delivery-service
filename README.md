# 🍕 Tony's Delivery Service

A frantic, one-thumb, portrait-mode 3-lane pizza delivery runner. You're Tony's delivery boy: follow the GPS, dodge traffic, keep the pizza hot, and don't miss your turn — or it's the long way around.

**Play:** https://shreyasthiagaraj.github.io/tonys-delivery-service/ (best on a phone, portrait) — or just open `index.html` in any browser.

## How to play

| Input | Action |
|---|---|
| Swipe ← / → (or arrow keys / A / D) | Change lanes; swipe toward the GPS arrow at an intersection to make the turn |
| Press & hold (or ↑ / W / Space) | Boost — arrive faster, pizza stays hotter, tip gets bigger |
| Tap ⏸ / P / Esc | Pause |
| M | Mute |

## Core loop

1. **Take an order** — customer, address, and a mini route map showing the turns ahead.
2. **Ride** — dodge cars, parked cop cars, roadwork barriers, and potholes. Boosting through a **speed trap** gets you fined.
3. **Follow the GPS** — swipe with the arrow inside the intersection window to turn. Miss it and the route gets longer ("RECALCULATING…").
4. **Collect** — 8 pizza slices fill the pizza meter → a whole pie = +200 and an extra pizza box (a life, max 3). Breadsticks are the persistent currency.
5. **Deliver** — tip = base fee + hot-pizza bonus + whole pies + best near-miss combo − missed turns. 1–3 stars. Next order is longer and faster.

Crashes cost a pizza box; lose all three and it's PIZZA DOWN (revive once per run for 🥖30 — this slot is the future rewarded-ad placement). Near misses build a combo with rising-pitch SFX — the fastest way to build score.

Tony shouts from the corner ("MAMMA MIA!", "the OTHER left!") via speech bubbles and, where the browser supports it, an actual Italian `it-IT` voice through the Web Speech API.

## Design decisions

- **3D voxel style** (Crossy Road family): a hand-rolled WebGL renderer — zero libraries — draws box-list voxel models with per-face shading, one neutral-white directional light, and far atmospheric fog (~120 units) with a distant skyline + sun/moon as a free skybox (the camera never moves in z; the world scrolls past it). All models are code: scooter + rider + pizza box, cars, cop cars with alternating light bars, striped barriers, glowing voxel pizza slices and breadsticks (additive gradient-disc fake bloom), procedural buildings, green curbs and sand grounds in bright natural colors.
- **Real turns**: the route is an L-shaped path — the road visibly bends 90° at the intersection ahead (with straight-ahead and wrong-arm filler streets making a true 4-way), and taking the turn sweeps the player's heading smoothly while the whole world rotates around the centered player. Implemented as a fixed-frame path transform (`makePlace`), not a camera trick.
- **2D HUD overlay** on a second canvas: pizza wheel, pizza-box lives, breadstick wallet, GPS pill, route-progress bar (scooter dot → pin with turn tick marks), heat bar, and Tony's speech bubble. Crisp at any DPI.
- **Italian-flag palette** — Tony Red `#E63946`, Basil Green `#2A9D5C`, Cheese Gold `#FFC93F`, Mozzarella Cream `#FFF6E3` over warm terracotta streets.
- **All audio is synthesized** — WebAudio step-sequencer plays a tarantella-flavored loop (tempo rises with level); SFX are synth blips/noise bursts. No audio files.
- **Levels are deliveries** — 20–60 second runs (hyper-casual sweet spot), death→retry is one tap, first delivery opens fail-proof (research: player must understand success within 10s).
- **Time-of-day cycling** — day / sunset / night sky, fog, and lighting every 3 levels (night adds a headlight pool) for visual variety in clips.

## Research findings baked in

(from the design-research pass; sources in the research brief)

- Lane change triggers on swipe **threshold-cross, not finger-up**; second swipe buffered mid-tween so lane 1→3 double-swipes never drop.
- Hitboxes ~30% smaller than sprites — responsiveness over strictness.
- Near-miss bonus + combo multiplier (Sonic Dash pattern) with escalating pitch/particles = the clip generator.
- Coin/stick trails double as steering guidance through gaps (teaches pathing).
- Revive-at-death is the genre's highest-converting rewarded placement → implemented as 🥖30 revive to model it.
- Speed-cap difficulty curve, breather chunks after dense sections, turn telegraphed ~2–3s ahead.

## Monetization roadmap (not yet wired)

1. **Rewarded video**: revive (already in-game as breadstick sink — swap/augment with ad), 2× tip on the delivered screen, timer-gated free gift on title.
2. **Skin economy**: 6 scooter skins now; tune so the first skin lands in session 1–2. Add pizza-box trails/outfits, then a Crossy-Road-style prize machine (100 🥖/spin, no duplicates).
3. **Missions/daily**: "P-I-Z-Z-A" letter hunt across runs; mission sets raise a permanent score multiplier (Subway Surfers pattern).
4. **TikTok content hooks**: comedic crash (slices everywhere), MAMMA MIA voice line, near-miss combos, "deliver in under 60s" challenge framing. Open every clip mid-action, never on a menu.

## Asset upgrade path (all catalogued with licenses)

Current build ships zero external assets. When upgrading to sprite art, the CC0 path is:

- **Kenney (CC0):** Racing Pack (top-down cars/roads), Roguelike Modern City (1k+ top-down city tiles), Road Textures, Food Kit (pizza w/ 2D renders), UI Pack, and audio packs (Impact Sounds, Digital Audio, Music Jingles) — kenney.nl
- **OpenGameArt:** "Bikes" (CC0 top-down motorbike — closest CC0 scooter found), Unlucky Studio top-down cars (incl. animated police car; credit appreciated), CC0 food icon sets
- **Music:** Kevin MacLeod "Bushwick Tarantella Loop" (CC-BY, attribution required) or Pixabay tarantella tracks (no attribution). Tarantella Napoletana itself is public domain — the current synth rendition is fully original.
- **Fonts:** Luckiest Guy (Apache 2.0), Bangers / Titan One (OFL) — must be subset + embedded (no CDN in the artifact sandbox).
- **Voice:** no CC0 "mamma mia" exists (meme clips are Mario rips — avoid); Web Speech API `it-IT` is the zero-asset option (current), or record original lines.

## Tech

Single self-contained HTML file (~2,300 lines): a dependency-free WebGL voxel renderer (mat4 math, one shader, box-list models baked to VBOs, fog + directional light, 3D cube particles) plus a Canvas 2D HUD overlay. devicePixelRatio-aware, portrait-first, `localStorage` persistence (`tonys_v1`), no build step, graceful message if WebGL is unavailable. Verified by a headless smoke test (stubbed DOM/GL driving the full state machine), a 90-second random-input monkey test in real Chrome, and screenshot review of every screen.
