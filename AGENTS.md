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
  heading. Unresolved `[[links]]` create notes on click; date-named
  links route through `openDay` so they land in their month folder and
  get calendar dots. Right-click a calendar day → "Carry over tasks"
  copies that day's unchecked tasks into the next day's note
  (`carryOverTasks`, dedupes, inserts under `## To do` if present).
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
  imports files to `attachments/`. Typing `:` opens the emoji shortcode
  popup (caret-anchored via a mirror div; capture-phase keydown so it
  beats the editor's own Enter/Tab handlers); `:name:` renders as an
  emoji in preview via the `EMOJI` map in `inline()`.
- **Print**: topbar Print → `#printSheet` overlay renders the note via
  `md()` into a fixed light-styled "page" (`#printDoc` has its own
  palette — paper is white regardless of theme). `@media print` uses
  `body:has(#printSheet.open)`/`#cheat.open` to hide everything but the
  open overlay; `@page` sets margins. Help → "Printable cheat sheet"
  opens `#cheat`, a half-letter card rendered from `SYNTAX`, `SHORTCUTS`
  and `EMOJI` — keep those arrays current and both the help modal and
  the cheat sheet update themselves. `resolveImages(container)` serves
  preview and print; blob URLs are tracked per container in `imgUrls`.
- **Right panel**: `#panel` holds app-level buttons (Theme, Markdown
  reference, Trash, About — equal width via a single-column grid);
  the topbar keeps only note-scoped inline-SVG icon buttons
  (`currentColor`, so they follow theme vars). Fixed width set by the
  Theme → "Panel width" slider (`dn-panel-w`); collapses to the
  `#panelTab` edge tab (`dn-panel-hide`). The settings popover offsets
  right by the panel width.
- **Settings persistence**: `localStorage` keys `dn-theme`, `dn-colors`
  (per-theme overrides — `{dark:{},light:{}}`; old flat format migrates
  onto the active theme), `dn-font`, `dn-spell`, `dn-side-w`,
  `dn-tree-open`, `dn-panel-w`, `dn-panel-hide`.

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
