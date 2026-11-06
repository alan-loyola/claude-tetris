# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris (HTML5 Canvas). No dependencies, no `package.json`, no build, no tests, no linter. The README and UI text are in Spanish; keep user-facing strings in Spanish.

## Running

Open `index.html` directly, or serve statically (e.g. `python -m http.server 8000`) and visit `http://localhost:8000`.

## Architecture

Three files: `index.html` (DOM + two canvases), `style.css`, and `game.js` (all logic, one global script with module-level mutable state).

- `game.js` looks up DOM elements by id at load time (`board`, `next-canvas`, `score`, `lines`, `level`, `overlay`, `overlay-title`, `overlay-score`, `restart-btn`). Renaming an id in `index.html` requires updating `game.js`.
- Board is a `ROWS × COLS` matrix of `0` or a color/piece index (1–7). The same index keys into `COLORS` and `PIECES`, so the piece shape matrices embed their own index as the cell value.
- Game state is a set of top-level `let` variables, reset in `init()`. Restart button calls `init()`.
- Flow: `init()` → `spawn()` → `requestAnimationFrame(loop)`. `loop` accumulates time into `dropAccum` and drops/locks when it exceeds `dropInterval`. `lockPiece()` = `merge()` → `clearLines()` → `spawn()`. `spawn()` calls `endGame()` if the new piece collides.
- Level/speed are recalculated inside `clearLines()` (`dropInterval = max(100, 1000 - (level-1)*90)`).
- Pause (`togglePause`) cancels/restarts the rAF loop and reuses the game-over overlay.
- Changing `COLS`, `ROWS` or `BLOCK` requires matching `width`/`height` on `<canvas id="board">` in `index.html` (`COLS*BLOCK` × `ROWS*BLOCK`). The next-piece canvas is 120×120 with a hardcoded 4×4 grid at 30px (`drawNext`).
