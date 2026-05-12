# Zeyad OS — Open Source Life Brain

An interactive personal "life brain" website for organizing Zeyad's goals, ideas, projects, skills, and story.

## Project Structure

```text
zeyad-os-life-brain/
├── index.html        # Main interactive website
├── brain-data.json   # Life brain data, nodes, and custom entries
└── README.md         # Project explanation and setup guide
```

## What This Is

Zeyad OS is a personal dashboard / hologram-style brain map. It organizes:

- Game development goals
- Live streaming goals
- AI and online work skills
- Website and creative projects
- Business ideas
- Personal growth notes
- Future plans and new updates

## How to Use

1. Open `index.html` in any browser.
2. Click the hologram brain nodes.
3. Add new brain entries in the update section.
4. Export your entries as JSON when you want a backup.
5. Import `brain-data.json` later to restore or move your data.

## How to Put It on GitHub

1. Create a new GitHub repository.
2. Upload:
   - `index.html`
   - `brain-data.json`
   - `README.md`
3. Go to **Settings → Pages**.
4. Set source to your main branch.
5. Your website will become public through GitHub Pages.

## Future Auto-Sync Idea

A standalone HTML file cannot automatically read every ChatGPT conversation by itself.

For full auto-sync later, connect:

- GitHub Pages or Vercel for hosting
- Supabase or Firebase for database storage
- A small API route to save new life-brain entries
- A manual or automated workflow that sends selected ChatGPT notes into the database

## Privacy Note

Only store information you actually want saved. This project is meant to be editable and personal.
