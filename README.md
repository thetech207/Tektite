# Tektite

A minimal Obsidian-style daily journal that runs entirely in your browser.
(Tektite: natural glass formed by meteorite impacts — Obsidian's cousin.)
One self-contained `index.html` file — no installation, no server, no
dependencies. Your notes are plain `.md` files in real folders on your disk,
fully compatible with Obsidian.

## Requirements

- A Chromium-based browser: **Chrome, Edge, Brave, Arc**, etc.
- Not supported: Firefox or Safari (they can't write files to disk from a
  web page).

## Getting started

1. Open `index.html` in your browser (double-click it, or bookmark the file
   path for one-click access later).
2. Click **Choose notes folder** and pick any folder — that's your vault.
3. The app opens today's note automatically. Start writing.

Your folder choice is remembered in the browser. Next visit it either opens
directly or asks for one click to re-grant access. If the folder is moved or
deleted you'll get a clear message and can pick a new one.

## Daily notes

- **Today** button, **‹ ›** arrows, and the **mini calendar** all open a
  day's note — creating it if needed.
- Notes are stored as `YYYY/MM - Month/YYYY-MM-DD.md`, e.g.
  `2026/09 - September/2026-09-19.md`.
- In the tree they display as `09-19-2026 - Saturday` and sort
  chronologically.
- New daily notes start with a `# Weekday, Month D, YYYY` heading.
- **Task rollover**: unchecked `- [ ]` items from your most recent daily
  note carry into the new one under `## To do`.
- Calendar dots mark days that have notes.

## Editing

- **Ctrl/⌘ + E** toggles markdown ↔ rendered preview. **Ctrl/⌘ + S** saves;
  notes also auto-save as you type. **Ctrl/⌘ + P** quick-opens a note by name.
- **Enter** on a list or `- [ ]` task line creates the next item (numbered
  lists increment). **Enter** on an empty item ends the list.
- **Tab / Shift+Tab** indents or unindents the current line or selection.
- **Auto-pairing**: `*`, `` ` ``, `(`, `{`, `"`, `'`, `_`, `~` insert their
  closer with the cursor between; wraps selected text; typing the closer
  types over it; Backspace inside an empty pair deletes both. (`[` is
  deliberately not paired so `- [ ]` checkboxes type cleanly.)
- Checkboxes are clickable in preview and write back to the file.
- Word count sits in the topbar.
- **Tabs**: open notes live in a tab strip across the top — click to switch,
  drag to reorder, × or middle-click to close. The active tab is highlighted
  with an accent bar and follows files through moves and renames. Clicking a
  note in the tree navigates the *current* tab; to open something in its own
  tab use right-click → **Open in new tab**, or click a day in the mini
  calendar. Notes open in **preview mode** — Ctrl/⌘+E to edit.
- The **Markdown reference** button (sidebar footer) lists every supported
  syntax with examples.

## Files & folders

- The sidebar shows your whole vault — every folder and file, not just
  dates. Folders start collapsed; expand/collapse state and sidebar width
  (drag the edge) persist between sessions.
- **Search** box filters by filename only.
- **+ Note / + Folder** create items via a path prompt (`a/b/note` nests).
- **Right-click** any item: new note/folder inside it, rename, move,
  move to `.trash` (hidden but kept on disk), or delete permanently.
- **Drag** items onto folders to move them; drop on empty space for root.
- **Attach** button, pasting, or dropping files onto the editor copies them
  into `attachments/` and inserts a link. Images embed; other files open in
  a new browser tab when clicked.
- `[text](other-note.md)` links navigate between notes in preview.
- `[[name]]` wikilinks work too — click to open the note anywhere in the
  vault (created at root if missing); `[[name|alias]]` sets display text.
- **Trash** button (sidebar footer): view `.trash`, restore items (date
  notes return to their month folder, others to root), or delete for good.

## Appearance

- **Theme** button (topbar): dark/light plus per-color pickers for
  background, panels, text, accent, etc. Settings persist per browser.
- **About** button (topbar): version, release date, credits, quickstart.
- **Spellcheck** toggle and a **preview font-size** slider live in the Theme
  panel (spellcheck off by default).
- Editor temp files (`.crswap`, `.swp`, `.tmp`, `~`, `.bak`) are hidden from
  the tree even though they exist on disk.

## Data & privacy

- Everything stays on your machine. The vault is read/written directly to
  disk; browser storage only remembers which folder you picked and your
  theme preferences.
- Sharing the app = sharing the single `index.html` file. Nothing personal
  travels with it.

## Using an existing Obsidian vault

Point "Choose notes folder" at your Obsidian vault — it just works. Notes,
folders, images and attachments all show up; `.obsidian` config is ignored;
`.trash` is shared with Obsidian. For the daily-note features (calendar dots,
Today button, task rollover) your daily notes must live at
`YYYY/MM - Month/YYYY-MM-DD.md`. Edits here are plain markdown — Obsidian
picks them up instantly, and vice versa.

## Known limits

- Markdown is a subset (no footnotes or math). YAML frontmatter is hidden,
  `==highlights==` render, and callouts show as plain blockquotes.
- `![[note]]` embeds link to the note rather than transcluding its content.
- Attachment links open in a browser tab — browsers can't launch the OS
  default app.
- Deleting removes real files (`.trash` keeps a copy); there is no undo.
