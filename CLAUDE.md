# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Underdark is a top-down 2D adventure game (Act 1: "The Disappearance") built as a single self-contained HTML file. No build tools, dependencies, or frameworks — just vanilla HTML5 Canvas and JavaScript. Open `index.html` directly in a browser to run.

## Architecture

Everything lives in `index.html` (~1700 lines). Key sections in order:

1. **Config & Colors** — tile size, speed constants, color palette (`C` object)
2. **Characters** (`CHARS`) — sprite definitions (hair, skin, colors, style)
3. **i18n** (`STRINGS`, `t()`) — bilingual EN/RU support, language stored in `localStorage` key `underdark-lang`
4. **Game State** — `gameState` drives the main loop: `title` → `playing` → `dialogue` → `transition` → `ending`
5. **Input** — touch joystick (left half of screen) + action button (right half), keyboard fallback (WASD/arrows + space/e/enter), mouse click for title screen
6. **Tile System** (`T` enum, `WALKABLE` set) — 19 tile types, collision via walkability set
7. **Maps** (`MAPS` object) — 3 maps built procedurally by `buildMap_*()` functions: `neighborhood` → `town` → `forest`. Each map has tiles, NPCs, exits (with optional conditions), and particle type
8. **Story Logic** — `storyFlags` object tracks progression; `checkAllTalked()` / `checkTownDone()` gate map transitions
9. **Rendering** — `drawTile()` switch for all tile types, `drawCharSprite()` for pixel-art characters, `drawObjectSprite()` for items/portal, `drawPortrait()` for dialogue box portraits
10. **Dialogue System** — typewriter effect with character portraits, driven by `dialogueQueue` array
11. **Game Loop** — `update(dt)` handles movement/collision/exits, `draw()` renders everything
12. **Screens** — `drawTitle()` (with language selector), `drawEnding()` (timed reveal sequence)

## Key Patterns

- **All text must go through `t(key)`** — add new strings to the `STRINGS` dictionary with both `en` and `ru` values
- **NPC dialogue** is defined inline in each map's `npcs` array as a `dialogue: () => [...]` function that returns an array of `{who, text}` objects. Use `t('dlg_...')` for text values
- **Map transitions** use exit tiles with optional `condition` callbacks checking `storyFlags`
- **Variable name `t`** is reserved for the translation function — use `tt` (or another name) for local time variables in draw functions to avoid shadowing
- Character names for `officer` and `shopkeeper` are localized in the dialogue renderer via `t('name_officer')` / `t('name_shopkeeper')`; other character names stay the same in both languages

## Development

No build step. To develop:
- Open `index.html` in a browser
- Edit the file, refresh to see changes
- `plot.txt` contains story outline notes (Turkish/English mix) for Acts 1–3
