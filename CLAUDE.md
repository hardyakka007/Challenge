# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A single-file browser game: **Space Khamenei Invaders** — a Space Invaders clone where the invaders display a face image (`Khaminai.jpg`).

## Running the game

Open `index.html` in a browser. If local images are blocked (Chrome `file://` restriction), serve locally:

```bash
python -m http.server 8080
# then open http://localhost:8080
```

## Architecture

Everything lives in `index.html` — one self-contained HTML/CSS/JS file with no build tools, dependencies, or bundler.

- **Canvas**: 800×550, all rendering via `requestAnimationFrame` loop calling `update()` then `draw()`
- **Game states**: `waiting` → `playing` → `dead`; level clears loop back through `nextLevel()` → `setupLevel()`
- **Key functions**:
  - `initGame()` — resets score/lives/level, calls `setupLevel()`, sets state to `playing`
  - `setupLevel()` — spawns 10×4 invader grid, 4 destructible barriers; scales difficulty via `invMoveInterval` and `invShootInterval` based on `level`
  - `update()` — player movement/shooting, invader movement (step+drop pattern), invader shooting, all collision detection via `rectOverlap()`
  - `draw()` — clears canvas, renders barriers (opacity reflects HP), invaders (image clipped to circle, red outline), player, bullets, ground line
- **Invader image**: Loaded as `<img id>` element, drawn with `ctx.clip()` arc to create circular frames. Falls back to dark red rectangle if image fails to load. Game loop starts in `invaderImg.onload/onerror`.
- **Barrier blocks**: Each barrier is an array of 7×7px blocks with `hp: 3`; damaged blocks render at reduced opacity; blocks with arch cutout at bottom center are omitted at spawn
- **Difficulty scaling**: `invMoveInterval = max(10, 60 - (level-1)*8)`, `invShootInterval = max(25, 80 - (level-1)*10)`, bullet speed `5 + level*0.5`, step size `12 + (level-1)*1.5`
- **Scoring**: `(INV_ROWS - row) * 10` per kill — back rows worth less, front rows worth more

## Sharing

To share the game, both `index.html` and `Khaminai.jpg` must be sent together. For a single self-contained file, embed the image as a base64 data URL inside `index.html`.

My favorite color is purple