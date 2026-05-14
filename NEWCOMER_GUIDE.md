# Newcomer Guide: Zeyad OS

This project is intentionally simple: a **single-page, client-side web app** that acts like a personal "life brain".

## What lives where

- `index.html`: the entire app (markup, styles, and JavaScript logic).
- `brain-data.json`: starter data shape and categories.
- `README.md`: high-level usage and deployment notes.
- `CLAUDE.md`: contributor-facing architecture and conventions.

## Mental model

- There is one central state object for the "brain".
- The app loads defaults immediately, then may seed from `brain-data.json` on first visit.
- Data is persisted to `localStorage` under a fixed key.
- Rendering is done with a few idempotent render functions that rebuild UI from state.
- Import/Export operates on the same normalized state shape.

## What to learn first

1. Read the `DEFAULTS` object in `index.html`.
2. Follow `loadState()` and `mergeWithDefaults(incoming)`.
3. Read `render()` and the three render helpers.
4. Trace entry creation flow: form submit -> sanitize -> persist -> re-render.
5. Trace import/export and reset actions.

## Important guardrails

- Keep the app single-file (`index.html`) unless explicitly asked otherwise.
- Keep `DEFAULTS` and `brain-data.json` node metadata synchronized.
- Render user strings safely (`textContent` / escaping).
- Preserve entry validation constraints (date + length limits).

## Good next improvements (without changing architecture)

- Add small inline comments above key state/render/import functions.
- Add a tiny debug mode toggle for viewing raw state.
- Add keyboard accessibility improvements for node navigation.
- Add very light smoke checks (manual checklist in docs).
