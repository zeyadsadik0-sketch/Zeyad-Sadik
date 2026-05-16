# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

**Zeyad OS — Open Source Life Brain.** A single-page, fully client-side personal dashboard ("hologram brain") for organizing goals, ideas, projects, skills, and notes. Designed to be opened directly from disk or hosted as static files (e.g. GitHub Pages). No build step, no dependencies, no server.

## Repository Layout

```
index.html                  # The main app: HTML + CSS + JS in one file
brain-data.json             # Seed data for the main app (used on first visit if localStorage is empty and the file is reachable)
game-promo.html             # Standalone trailer/promo page
game-trailer.html           # Standalone trailer/promo page
tactical-fps-trailer.html   # Standalone trailer/promo page
README.md                   # User-facing project description
```

There is no package manager, no bundler, no test framework, and no lint config. Do not introduce any unless explicitly requested — the project's value is being a set of static HTML files that runs anywhere.

### Standalone trailer pages

Sibling `*-trailer.html` / `*-promo.html` pages live alongside `index.html`. Each is its own self-contained, single-file HTML page (inline CSS, inline JS) and is independent of the Zeyad OS brain app — they don't share state, scripts, or styles with `index.html`. When adding a new standalone page, follow the same single-file rule **per page** rather than splitting one page across multiple files.

## Run / Preview

- Open `index.html` directly in a browser, **or**
- Serve the folder over HTTP so the `fetch("brain-data.json")` seed works:
  - `python3 -m http.server 8000` then visit `http://localhost:8000/`
- No build, install, lint, or test commands exist.

When opened via `file://`, the `fetch` for `brain-data.json` will typically be blocked by the browser; the app silently falls back to the in-code `DEFAULTS`. This is intentional — do not add error UI for it.

## Architecture (one-pager)

All app logic lives in the IIFE at the bottom of `index.html`. Key pieces:

- **State shape** matches `brain-data.json`: `{ owner, version, updatedAt, nodes: [{ id, label, icon, color, description, entries: [{ title, note, date }] }] }`. Node `id`s are stable string keys; `DEFAULTS` defines the canonical set.
- **Persistence**: `localStorage` under the key `zeyad-os-brain-v1`. `loadState()` returns defaults synchronously, then asynchronously seeds from `brain-data.json` on first visit only (when no localStorage entry exists).
- **`mergeWithDefaults(incoming)`**: the single normalization path used by both initial load and Import. Always merges incoming nodes onto the `DEFAULTS` set keyed by `id`, so unknown ids are tolerated and missing ones get default labels/colors. Entries pass through `sanitizeEntry` (trims, clamps lengths, validates date format).
- **Rendering** is three idempotent functions — `renderNodes()`, `renderCategorySelect()`, `renderDetail()` — orchestrated by `render()`. They rebuild DOM from `state`; there is no diffing or virtual DOM. After mutating `state`, call `persist()` and the relevant render function(s).
- **Node layout**: nodes are positioned around the central core with CSS custom properties `--a` (angle, `360 / count * i`) and `--r` (radius). The bob animation reuses the same `transform` chain, so any change to positioning must update both the static rule and the `@keyframes bob` rule together.
- **Import/Export**: Export serializes the full `state` to a downloaded JSON file. Import reads a file, parses, runs it through `mergeWithDefaults`, persists, and re-renders. Reset clears localStorage and reverts to `DEFAULTS`.

## Conventions

- **Single-file per page.** Each HTML file in the repo (`index.html` and each standalone `*-trailer.html` / `*-promo.html`) keeps its HTML, CSS, and JS inline in that one file. Do not split a page across separate JS/CSS files and do not add a build step. Adding *new* standalone sibling pages is fine; splitting an existing one is not.
- **No external runtime dependencies for the main brain app.** `index.html` must run with no CDN scripts, no web fonts, and no required network assets. If it references a local image, commit the image to the repo — never leave a broken `url(...)` pointing at a missing file.
- **Standalone trailer pages may reference remote images** (e.g. CDN-hosted scene thumbnails) — they're allowed to require a network connection to render fully. When doing so: prefer stable/long-lived URLs over temporary ones (generated previews tend to expire), and any *local* image refs must still be committed rather than left broken. Scripts, fonts, and other code dependencies remain forbidden everywhere.
- **All user-supplied strings** rendered into the DOM must go through `textContent` or `escapeHtml()` — never interpolate untrusted strings into `innerHTML`. The existing entry/title rendering in `index.html` follows this; preserve it.
- **`DEFAULTS` in `index.html` and `brain-data.json` should stay in sync** on the node `id`, `label`, `icon`, `color`, and `description` fields. When adding or renaming a category, update both. (Applies to the main brain app only — standalone trailer pages don't use this data.)
- **Date format**: always `YYYY-MM-DD` (validated by `sanitizeEntry`).
- **Length caps**: title 120 chars, note 1000 chars. Mirror these in any new input.

## Git

- Default development branch for Claude-authored work: `claude/add-claude-documentation-0YjOj` (or the branch specified in the current task).
- `main` is the deployment branch (GitHub Pages source per the README).
