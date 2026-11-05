# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A browser platformer used as a CS 1101 teaching template. Students copy it, then edit only the four files in `student/` (numbered "HOLE 1–7" in comments, matching the assignment in `README.md`; Day 2 work is in `CHALLENGES.md`). The `engine/` folder is the finished machinery students are told not to touch. Keep student-facing comments and error text in plain, non-programmer language.

## Running

No build, package manager, linter, or tests. Plain JS with globals, no modules.

- Serve: `python3 -m http.server 8000` (what the devcontainer runs), then open `http://localhost:8000`. Opening `index.html` directly also works.
- `?level=3` starts on a given level. In-game keys: `T` tuning panel, `H` hitboxes, `N` next level, `R` restart.

## Architecture

- **Description vs. machinery.** `student/*.js` define data only (`TUNING`, level text maps, enemy/pickup descriptions, a `LEGEND` mapping map letters to things, theme colors). The engine interprets them. Prefer adding new behavior as new engine-supported fields/options rather than code in `student/`.
- **Load order matters.** `index.html` loads scripts via `document.write` from a `FILES` list (errors → student → spec → input → sfx → world → actors → render → panel → validate → game), with a `?v=Date.now()` cache-buster over http. A new engine file must be added to that list, and everything shares globals (e.g. `Validate`, `TUNING_SPEC`).
- **`engine/spec.js` is the contract.** It defines allowed tuning keys with defaults/ranges (also used for the tuning panel sliders), movement types, shapes, legend kinds, tile names, and enemy/pickup field names and required fields. Adding a tuning value, enemy field, or movement type means updating `spec.js`, `validate.js` (friendly errors, typo suggestions via `closest`), the relevant actor/render code, and the comments in the matching `student/` file.
- **`engine/validate.js` runs before the game starts.** Problems surface as a red box (fatal) or yellow bar (warning) with file/line/fix hints; `engine/errors.js` converts uncaught crashes into the same friendly style. Any new student-facing input needs validation messages written for beginners.
- **Runtime:** `world.js` (map text → tiles, collision), `actors.js` (player/enemy/pickup behavior), `render.js` (canvas drawing), `game.js` (main loop, levels, score/lives), `panel.js` (live tuning sliders, not persisted), `input.js`, `sfx.js` (synthesized tones, no audio files).
- `docs/MAP.md` is a file-by-file tour and lists the engine's size and file tree; update it if files are added or removed.

## Git

Work branch is `platformer_branch`; `main` is the PR target and is what GitHub Pages serves.
