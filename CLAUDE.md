# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A collection of browser-based games — each is a **single self-contained HTML file** with no build step, no dependencies, and no server required. Open any file directly in a browser.

## Running the Games

```bash
# Open in default browser (Windows)
powershell.exe -Command "Invoke-Item 'cycling.html'"
powershell.exe -Command "Invoke-Item 'tictactoe.html'"
```

There are no tests, no linter, and no package manager. Verification is done by opening the file in a browser and playing it.

## Git & GitHub Workflow

Remote: `https://github.com/antaresng/claudetest`

After every meaningful change:
1. Stage specific files (never `git add .` blindly)
2. Commit with a clear message
3. Push to `origin master`

```bash
git add <file>
git commit -m "description"
git push origin master
```

Credentials are handled automatically via `gh auth setup-git` (no password prompts).

## Architecture

Each game follows the same pattern:

- **Single HTML file** — all HTML, CSS, and JS inline, no external resources
- **Canvas-based rendering** — `requestAnimationFrame` game loop, `ctx` for all drawing
- **No frameworks** — vanilla JS only
- **Color palette** shared across games: `#1a1a2e` (bg), `#e94560` (accent red), `#a8dadc` (teal), `#f0a500` (amber), `#0f3460` (deep blue)

### cycling.html

Top-down sidewalk cyclist game.

- **Constants** at top: `UNIT=48` (lane width), `CANVAS_W=360`, `CANVAS_H=600`, `WALL_LEFT=36`, `WALL_RIGHT=324`
- **State machine**: `gameState` string — `idle | running | paused | gameover`
- **Player** snaps to lane index (0–5); `targetX` lerps to lane center each frame
- **Pedestrians** array — each has `vx/vy` physics, `collisionCooldown`, `bellJumping` state
- **`update(dt)`** runs physics, spawn logic, collision detection, score accumulation
- **`render()`** paints layers in order: walls → tiles → lane lines → pedestrians → player → flash overlays → state screens
- **HUD** is an HTML `div` overlaid via `position: absolute` (not drawn on canvas)
- `dt` is capped at 50ms and all velocities are scaled by `dt / 16.667` for frame-rate independence

### tictactoe.html

Tic Tac Toe with computer opponent.

- AI difficulty 0–10: level 0 = random, level 10 = minimax (unbeatable), levels 1–9 = blended probability
- Two modes: PvP and PvC
- Score persists across games within a session
