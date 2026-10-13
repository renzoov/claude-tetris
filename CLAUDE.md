# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris (HTML5 Canvas). No dependencies, no `package.json`, no build, no linter, no tests. The README and UI strings are in Spanish; keep user-facing text in Spanish.

## Running

Open `index.html` directly, or serve statically (e.g. `python -m http.server 8000`) and visit `http://localhost:8000`. Verify changes manually in the browser.

## Architecture

Three files: `index.html` (DOM + canvases), `style.css`, and `game.js` (all logic, one global script with `'use strict'`, loaded at the end of `<body>`).

- `game.js` holds all game state in top-level `let` variables (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, ...), reset by `init()`. The restart button calls `init()` again.
- Board is a `ROWS×COLS` matrix of `0` or a piece-type index 1–7. That same index selects the color in `COLORS` and the shape in `PIECES`, so the two arrays must stay aligned (index 0 is `null`).
- Piece lifecycle: `spawn()` promotes `next` to `current` and calls `endGame()` if it collides immediately. Locking goes `lockPiece()` → `merge()` → `clearLines()` → `spawn()`.
- The game loop (`loop`) is `requestAnimationFrame`-driven and accumulates time against `dropInterval`. Pause and game over stop it with `cancelAnimationFrame(animId)`. `togglePause()` restarts it by calling `loop` directly, so any new stop/start path must keep `animId` and `lastTime` consistent.
- Input is a single `keydown` handler using `e.code`. Rotation is `rotateCW` plus horizontal kicks `[0, -1, 1, -2, 2]` in `tryRotate`.
- Changing `COLS`, `ROWS` or `BLOCK` also requires updating the `width`/`height` attributes of `<canvas id="board">` in `index.html` (`COLS*BLOCK` × `ROWS*BLOCK`). The next-piece canvas is 120×120 and `drawNext` assumes a 4×4 grid of 30px blocks.
