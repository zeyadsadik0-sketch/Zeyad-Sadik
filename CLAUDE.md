# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

**Zeyad OS — Open Source Life Brain.** A single-page, fully client-side personal dashboard ("hologram brain") for organizing goals, ideas, projects, skills, and notes. Designed to be opened directly from disk or hosted as static files (GitHub Pages). No build step, no dependencies, no server.

The repo also contains an unrelated game-design knowledge base (`reaper-brain/`) and two standalone marketing pages (`game-promo.html`, `game-trailer.html`) for that game. These are independent artifacts — they do not share code or state with `index.html`.

## Repository Layout

```
index.html          # The Zeyad OS app: HTML + CSS + JS in one file
brain-data.json     # Seed data for the app (loaded on first visit if reachable)
game-promo.html     # Standalone marketing page for "REAPER: CHECKPOINT TWELVE"
game-trailer.html   # Standalone trailer page for the same game
reaper-brain/       # Obsidian-style markdown design docs for the game (INDEX.md is the hub)
README.md           # User-facing project description
NEWCOMER_GUIDE.md   # Short onboarding walkthrough
.github/workflows/  # Jekyll GitHub Pages deploy on push to main
```

There is no package manager, bundler, test framework, or lint config. Do not introduce any unless explicitly requested — the project's value is being a single static HTML file that runs anywhere.

## Run / Preview

- Open `index.html` directly in a browser, **or**
- Serve the folder over HTTP so the `fetch("brain-data.json")` seed works:
  - `python3 -m http.server 8000` then visit `http://localhost:8000/`
- No build, install, lint, or test commands exist.

When opened via `file://`, the `fetch` for `brain-data.json` will typically be blocked by the browser; the app silently falls back to the in-code `DEFAULTS`. This is intentional — do not add error UI for it.

## Deployment

`.github/workflows/jekyll-gh-pages.yml` runs Jekyll on push to `main` and publishes to GitHub Pages. The source files contain no Jekyll templating, so the build is effectively a static copy.

## Architecture (one-pager)

All app logic lives in the IIFE near the bottom of `index.html` (starts around line 600). Key pieces:

- **State shape**: `{ owner, version, updatedAt, nodes: [{ id, label, icon, color, description, entries: [{ title, note, date, status }] }] }`. Node `id`s are stable string keys; `DEFAULTS` defines the canonical set. Entry `status` is one of `"todo" | "doing" | "done"` (defaults to `"todo"`).
- **Persistence**: `localStorage` under the key `zeyad-os-brain-v1`. `loadState()` returns defaults synchronously, then asynchronously seeds from `brain-data.json` on first visit only (when no localStorage entry exists).
- **`mergeWithDefaults(incoming)`**: the single normalization path used by both initial load and Import. Always merges incoming nodes onto the `DEFAULTS` set keyed by `id`, so unknown ids are tolerated and missing ones get default labels/colors. Entries pass through `sanitizeEntry` (trims, clamps lengths, validates date format, normalizes `status`).
- **Rendering** is four idempotent functions — `renderNodes()`, `renderCategorySelect()`, `renderDetail()`, `renderMindmap()` — orchestrated by `render()`. They rebuild DOM from `state`; there is no diffing or virtual DOM. After mutating `state`, call `persist()` and then `render()` (or the relevant render function).
- **Node layout**: nodes are positioned around the central core with CSS custom properties `--a` (angle, `360 / count * i`) and `--r` (radius). The bob animation reuses the same `transform` chain, so any change to positioning must update both the static rule and the `@keyframes bob` rule together.
- **Import/Export**: Export serializes the full `state` to a downloaded JSON file. Import reads a file, parses, runs it through `mergeWithDefaults`, persists, and re-renders. Reset clears localStorage and reverts to `DEFAULTS`.

## Conventions

- **Keep it single-file.** Do not split `index.html` into separate JS/CSS files or add a build step.
- **No external runtime dependencies.** No CDN scripts, no fonts, no images required.
- **All user-supplied strings** rendered into the DOM must go through `textContent` or `escapeHtml()` — never interpolate untrusted strings into `innerHTML`. Existing entry/title rendering follows this; preserve it.
- **`DEFAULTS` in `index.html` and `brain-data.json` should stay in sync** on the node `id`, `label`, `icon`, `color`, and `description` fields. When adding or renaming a category, update both.
- **Date format**: always `YYYY-MM-DD` (validated by `sanitizeEntry`).
- **Length caps**: title 120 chars, note 1000 chars. Mirror these in any new input.
- **Entry status values**: only `"todo"`, `"doing"`, `"done"` — `sanitizeEntry` falls back to `"todo"` for anything else.

## `reaper-brain/` notes

Obsidian-style markdown vault for the game design (tactical FPS set in Iraq, 2004–2007). `INDEX.md` is the hub. Cross-references use `[[Wiki Link]]` syntax targeting note titles (not file paths) — preserve this style; do not rewrite to standard `[text](path)` markdown links.

## Git

- Default development branch for Claude-authored work: `claude/add-claude-documentation-KE1f0` (or the branch specified in the current task).
- `main` is the deployment branch (auto-deploys via the Jekyll workflow above).
