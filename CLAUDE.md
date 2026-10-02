# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running

Vanilla HTML5 Canvas game: no build, bundler, dependencies, linter or tests. Open `index.html` in a browser, or serve it with `npx serve .` (http://localhost:3000). Reload the page to see changes.

## Architecture

All game logic lives in a single file, `game.js` (loaded by `index.html` onto an 800×600 `#canvas`). Code comments and UI strings are in Spanish; keep that convention.

- **Entities** are classes (`Ship`, `Bullet`, `Asteroid`, `Particle`) each with `update(dt)` and `draw()`, and a `dead` flag. Dead entities are removed by `filter` in `update()` rather than spliced in place.
- **Global state** (`ship`, `bullets`, `asteroids`, `particles`, `score`, `lives`, `level`, `state`) is module-level `let`, reset by `initGame()`. `state` is `'playing' | 'dead' | 'gameover'` and `update()` branches on it first. `'dead'` is a 2s respawn timer during which asteroids keep moving.
- **Loop**: `requestAnimationFrame` → `update(dt)` → `draw()`, with `dt` in seconds clamped to 0.05. All speeds are in px/s (or rad/s) multiplied by `dt`.
- **World is toroidal**: positions use `wrap(v, max)` using the constants `W`/`H`. Particles are the exception and do not wrap.
- **Input**: `keys[code]` is held state; `pressed(code)` is a consume-once edge trigger (used for Space so shots aren't auto-fired by holding).
- **Asteroids** are tuned through the parallel arrays `RADII`, `SPEEDS`, `POINTS`, indexed by size 1–3 (index 0 unused). Note size 3 (large) gives the fewest points. `split()` spawns two of `size - 1`. Ship collisions use `a.radius * 0.82` as a forgiving hitbox.
- Clearing all asteroids calls `nextLevel()`, which spawns `3 + level` large asteroids outside a safe radius from the center.

## Note on README

`README.md` mentions power-ups and a "shooting star" asteroid type, but these are not implemented in `game.js`.
