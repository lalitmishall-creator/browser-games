# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working in this repository.

## Project Overview

A collection of self-contained browser games. Each game is a **single `.html` file** — no build tools, no dependencies, no server required. Open in any browser directly from the filesystem.

## Workflow

- **Run a game:** `open shooter.html` or `open tictactoe.html` (macOS), or just double-click the file.
- **Deploy:** No build step. The file itself is the artifact.
- **Git:** Commit and push after every meaningful change. `git push` goes to `origin/main` on GitHub (`lalitmishall-creator/browser-games`).

## Architecture Pattern

All games follow the same pattern — everything lives inside one `.html` file:

```
<style>   — layout only (body centering, canvas sizing)
<canvas>  — single element, all visuals drawn programmatically
<script>  — all game logic, no external libraries
```

**shooter.html** internal structure (sections separated by banner comments):
- **Canvas Setup** — fixed 800×600 logical resolution, CSS-scaled to viewport
- **Game States** — `STATE` enum; `gameState` drives the main loop dispatch
- **Audio** — lazy-init Web Audio API; all SFX synthesized via `playTone(freq, type, duration)`
- **Input** — `keys{}` map (keydown/keyup) + `mouse{x,y,down}`; `handleKeyPress()` routes per-state
- **Level Definitions** — `LEVELS[]` array of wave configs; `ENEMY_CONFIGS` holds per-type stats
- **Player** — singleton object; `updatePlayer(dt)` / `drawPlayer()`
- **Bullets** — shared `bullets[]` array for player and enemy projectiles; `fromPlayer` flag differentiates
- **Enemies** — `enemies[]` array; type-specific AI in `updateEnemies(dt)`; `killEnemy(idx)` handles scoring + death
- **Particles** — `particles[]` array; `isCasing` / `isRect` flags select draw path
- **Wave/Level System** — `spawnQueue[]` timed by `spawnTimer`; `waveBreak` flag triggers 3s rest between waves
- **HUD + Screens** — each state has its own `draw*()` function called from the main loop
- **Game Loop** — `requestAnimationFrame` loop; `dt` capped at 50ms to prevent spiral-of-death on tab blur

**Collision:** Circle-circle only — `dx²+dy² < (r1+r2)²`. No spatial partitioning.

**Coordinates:** Canvas logical px (800×600). Mouse coordinates are scaled from CSS pixels in the `mousemove` handler.

**Persistence:** `localStorage` key `dzHighScore` (shooter only).

## Conventions

- No external assets — all sprites drawn with Canvas 2D primitives (rects, arcs, fills).
- Retro pixel aesthetic: `image-rendering: pixelated` on canvas; monospace fonts; scanline overlay every 4px.
- `dt`-based movement throughout (multiply velocity by delta time, never use fixed increments).
- New games follow the same single-file pattern as the existing ones.
