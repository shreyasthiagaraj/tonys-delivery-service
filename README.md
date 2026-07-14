# 🍕 Tony's Delivery Service

A frantic, one-thumb, portrait-mode 3-lane pizza delivery runner. You're Tony's delivery boy: beat the clock, grab every breadstick, and don't hit ANYTHING — one crash and the pizza's on the pavement.

**Play:** https://shreyasthiagaraj.github.io/tonys-delivery-service/ (best on a phone, portrait) — or just open `index.html` in any browser.

## How to play

| Input | Action |
|---|---|
| Swipe ← / → (or arrow keys / A / D) | Move exactly ONE lane per swipe; a held pan steps lane-by-lane with a settle pause between steps |
| Press & hold (or ↑ / W / Space) | Boost — the only way to beat tight clocks |
| Tap ⏸ / P / Esc | Pause |
| M | Mute |

## Core loop

1. **Take an order** — customer, address, distance, and a delivery deadline.
2. **Beat the clock** — a countdown timer is the run; boost banks time, and the last 5 seconds tick audibly.
3. **Collect breadsticks** — the only pickup and the persistent currency; trails double as steering guidance.
4. **Don't hit anything** — one collision ends the run with a slow-tumble crash animation (cars/cops/barriers). Potholes just stumble you and shake loose a few breadsticks; boosting past a speed trap costs sticks.
5. **Stars (1–3)** at delivery from a blend of time remaining and breadsticks collected vs. available. Fail states: PIZZA DOWN (crash) or PIZZA'S COLD (timeout) — the revive slot offers a restart-in-place or +15 seconds for 🥖30.

Stages are straight for now — the core game and visuals come first; turns return later.

## Design decisions

- **Top-down voxel style** (reference-driven): a hand-rolled WebGL renderer — zero libraries — draws box-list voxel models under a steep chase camera (~72° pitch, ground fills the frame, no horizon). Neutral-white light, soft distance haze. All models are code: scooter + rider + pizza box, cars with glass cabins and glowing taillights, cop cars with alternating light bars, striped barriers, glowing breadsticks, palms, market stalls with green awnings, green curb blocks on stone sidewalks over orange sand.
- **Real bloom post-processing**: scene renders to a framebuffer, a luminance bright-pass extracts highlights at quarter res, two-pass Gaussian blur, additive composite with a soft vignette. Deliberate emissives (taillight/light-bar/blinker/boost-flame glow discs, breadstick halos with orbiting sparkles) feed the bloom; the scooter leaves a trail of white cross-shaped exhaust puffs on the road.
- **2D HUD overlay** on a second canvas, reference-styled: gold pizza medallion, breadstick pill, countdown timer pill (green→amber→pulsing red), gold route-progress bar (scooter dot → pin), chunky centered score, Tony's speech bubble, rounded-square pause. Crisp at any DPI.
- **Italian-flag palette** — Tony Red `#E63946`, Basil Green `#2A9D5C`, Cheese Gold `#FFC93F`, Mozzarella Cream `#FFF6E3` over desert sand and blue-grey asphalt.
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
