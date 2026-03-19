# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A collection of self-contained browser games — each is a **single HTML file** with embedded CSS and JavaScript. No build tools, no package manager, no dependencies. Open any file directly in a browser to play.

## Running the Games

```bash
open tictactoe.html   # macOS
open shooter.html
```

Or just double-click the files in Finder / drag into a browser tab. There is no server, build step, or install process.

## Git & GitHub Workflow

After every meaningful change, commit and push:

```bash
git add <file>
git commit -m "short imperative summary"
git push
```

Remote: `https://github.com/koanguy/ClaudeCodeTest` (branch: `main`)

Commit message convention: short imperative subject line (≤72 chars), optional blank line + bullet details for non-obvious changes. One logical change per commit.

## Architecture

### Single-file pattern

Each game is fully self-contained: `<style>` block → HTML structure → `<script>` block, all in one `.html` file. No external assets, fonts, or scripts.

### tictactoe.html

DOM-based rendering (no canvas). Game logic is ~70 lines:
- `board[]` array (9 cells) + `current` player + `scores` object as the only state
- `checkWin()` iterates the 8 hardcoded `WINS` combinations
- All rendering is direct DOM manipulation (`.textContent`, `.classList`)

### shooter.html

Canvas-based (800×600), `requestAnimationFrame` game loop with delta-time (`dt`) physics. Organized into clearly labeled sections:

| Section | Key details |
|---|---|
| **Constants & Config** | `COLORS`, `STATES`, `ENEMY_TYPES`, `LEVELS` — tweak balance here |
| **Classes** | `Player`, `Enemy`, `Bullet`, `Particle` — each has `update(dt)` and `draw()` |
| **Level Manager** | `startGame()`, `spawnWave()`, `checkWaveComplete()`, `nextLevel()` |
| **UI / HUD** | `drawGrid()`, `drawHUD()`, `drawMenuScreen()`, `drawLevelComplete()`, `drawGameOver()`, `drawCrosshair()` |
| **Input** | `keys{}` map (keyboard) + `mouse{x,y,clicked}` updated via canvas event listeners |
| **Game Loop** | `update(dt)` → collision detection → state transitions; `render(dt)` → draw everything |

Enemy behavior is type-driven: `GRUNT`/`TANK` walk straight, `RUNNER` zigzags, `SHOOTER` stops at range and fires `enemyBullets`. All collision is circle-based (`dist()` radius check).

To add a new enemy type: add an entry to `ENEMY_TYPES`, add it to the relevant `LEVELS[n].types` arrays, and handle any special `update()` logic inside `Enemy.update()` via the `this.typeName` switch pattern already used there.

To add a new level: append to the `LEVELS` array — `waves`, `counts` (one per wave), and `types` (pool to randomly pick from).
