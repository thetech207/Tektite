# Tektite — agent notes

Single-file Obsidian-style markdown journal. Everything lives in
`index.html` (HTML + CSS + JS inline) — no build, no dependencies, no server
required. `favicon.svg` is a static copy of the favicon design; the live
favicon is a PNG data-URI drawn on a canvas from `--accent` by
`updateFavicon()` in `index.html` (Edge won't reliably display
dynamically-set SVG favicons, hence PNG).

## Architecture

- **Vault access**: File System Access API (`showDirectoryPicker`,
  `getDirectoryHandle`, `createWritable`). Chromium only. The directory
  handle is persisted in IndexedDB (`daily-notes` DB, `kv` store) and
  re-permissioned on load; stale handles fall back to the welcome screen.
- **Daily notes**: convention `YYYY/MM - Month/YYYY-MM-DD.md` via `parts()`.
  Tree labels render `MM-DD-YYYY - Weekday`. New date notes get a `#`
  heading plus `## To do` rollover of unchecked tasks from the most recent
  prior daily note (`rolloverTasks`, 14-day lookback).
- **Tabs**: `tabs[]` of `{path, handle, content}`; `curTab`/`curHandle`/
  `curPath` mirror the active tab. `openNote(path, create, newTab)` is the
  single entry point — tree clicks reuse the current tab, calendar clicks
  and context-menu "Open in new tab" force a new one.
- **Tree**: `scanDir()` recurses the vault; skips dot-folders (`.obsidian`,
  `.trash`) and temp files (`.crswap`, `.swp`, `~`, etc.); `attachments`
  sorts last. Expand/collapse persists in `localStorage` key `dn-tree-open`.
- **Indexes**: rebuilt each `refreshTree` — `noteIndex` (basename → path)
  for `[[wikilinks]]`, `fileIndex` (all files) for image/attachment
  resolution, `notePaths` for Ctrl+P quick-open, `existingDates` for
  calendar dots.
- **Full-text search**: Ctrl+Shift+F modal. `contentIndex` maps path →
  `{mtime, size, text}`, refreshed incrementally by `ensureContentIndex()`
  on open (open tabs contribute their live buffer; `save()` keeps the
  entry fresh). Results render one row per note — Titles group then
  Content group (heading match → mtime desc), `› heading` breadcrumb,
  `<mark>`-highlighted snippet, "+N more" expander. Enter opens the note
  in a new tab (or focuses the existing one) and `jumpToMatch()`
  selects/scrolls the line in the editor or flashes the matching
  element in preview.
- **Markdown**: hand-rolled subset in `md()`/`inline()` — headings, lists
  (incl. tasks + nesting), quotes, code, tables, `==highlight==`,
  `[[wiki]]`, `![[img]]`. YAML frontmatter is skipped. Checkbox toggles in
  preview write back to source.
- **File ops**: move/copy via copy-then-delete (`moveEntry`, `copyDir`);
  `.trash/` hides deleted items (restorable via Trash modal — date notes
  return to their month folder); `uniqueName` dedupes.
- **Editor behaviors**: Enter continues lists/tasks (empty item → break
  out), Tab/Shift+Tab indents, auto-pairs `* ` `` ` `` `_` `~` `(` `{`
  quotes — `[` deliberately excluded (checkbox ergonomics). Paste/drop
  imports files to `attachments/`.
- **Settings persistence**: `localStorage` keys `dn-theme`, `dn-colors`,
  `dn-font`, `dn-spell`, `dn-side-w`, `dn-tree-open`.

## Conventions

- Keep it dependency-free and single-file; all UI/CSS/JS stays in
  `index.html`.
- Themeable colors must be CSS vars under `:root` and listed in `COLOR_VARS`
  so the Theme panel exposes them.
- `setStatus()` for transient messages; `confirm()` for destructive ops.

## Verify

- No build/test suite. Serve with `python3 -m http.server` in this dir and
  exercise the UI in a Chromium browser, or open `index.html` via `file://`.
- Repo: github.com/thetech207/Tektite (origin/main). Commit + push when the
  user asks to publish.
