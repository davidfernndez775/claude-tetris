# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris (HTML5 Canvas + CSS). No dependencies, no `package.json`, no build, no linter, no tests. The README and UI text are in Spanish.

## Running

Open `index.html` directly, or serve statically (e.g. `python -m http.server 8000`) and visit `http://localhost:8000`. Verify changes by playing in the browser.

## Architecture

Three files: `index.html` (DOM + canvases), `style.css`, and `game.js` (all logic, loaded as a classic script with `'use strict'`, no modules).

`game.js` keeps all state in module-level `let` variables (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, `animId`, ...) that `init()` resets. Key points spanning the file:

- Board is a `ROWS × COLS` matrix of `0` or a color/piece index 1–7; `PIECES` and `COLORS` are indexed by the same number (index 0 is `null`).
- Pieces are square matrices; rotation is `rotateCW` (transpose + reverse), applied via `tryRotate`, which tries horizontal kicks `[0, -1, 1, -2, 2]`.
- The `requestAnimationFrame` `loop` accumulates `dropAccum` and gravity-drops the piece when it reaches `dropInterval`. `draw()` runs every frame.
- Piece lifecycle: `lockPiece()` → `merge()` → `clearLines()` (updates score/level/`dropInterval`) → `spawn()`. `spawn()` calls `endGame()` if the new piece collides immediately.
- `softDrop`/`hardDrop` award score directly (1/row and 2/row); the keydown handler and `clearLines` call `updateHUD()`.
- Pause and game over both cancel the animation frame and reuse the same `#overlay`; restart button re-runs `init()`.

Changing `COLS`, `ROWS` or `BLOCK` requires updating the `width`/`height` attributes of `<canvas id="board">` in `index.html` to `COLS×BLOCK` by `ROWS×BLOCK`. The next-piece canvas (`#next-canvas`, 120×120) is drawn with its own 30px block size in `drawNext`.
