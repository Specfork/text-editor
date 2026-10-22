# View

**Status:** Draft · **Prefix:** VEW

## Purpose

How text is shown: zoom, word wrap, invisible characters, margins, guides and the status bar.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| VEW-1 | Zoom per view: level −10..+20 added to the base font size in points (minimum shown size 2 pt); `Mod+=` / `Mod+-` one step, `Mod+0` resets to 0, `Mod+wheel` steps; the level shows in the status bar while not 0. Optional sync of both views (default off). | `Mod+=` 3 times → level +3 |
| VEW-2 | Word wrap toggle (`Alt+Z`, default off) wraps at the view's width on word boundaries (or any character when a word is wider); wrapped parts may keep the first part's indentation (setting `wrap_indent`, default on). Line numbers and `Mod+G` count file lines, not wrapped rows. | 200-char line, wrap on → several rows, one line number |
| VEW-3 | Show whitespace (spaces as dots, tabs as arrows), show line endings (`CR`, `LF`, `CRLF` markers), and show all; each a toggle, default off. Control and invalid bytes are always visible ([FIL-4](files.md)). | show line endings in a mixed file → both marker kinds |
| VEW-4 | Margins: line numbers (default on, width grows with line count), bookmarks, folding. Indent guides (default on) at each tab stop within leading whitespace. Edge column (default off, column 80, setting 1..1000) as a line or background colour. Current line highlight (default on). | 10,000-line file → numbers fully visible |
| VEW-5 | Status bar: language name, document length in characters and line count, `Ln`, `Col` (column counts tabs to the next tab stop), `Sel` (characters and lines; with several selections, their totals and count), line ending type, encoding, insert or overwrite. Clicking the line ending or encoding opens the convert or reinterpret menu. | select 2 lines of 5 chars → `Sel: 11 \| 2` with LF |
| VEW-6 | Full screen `F11` hides menus and toolbars; distraction-free mode also hides tabs and margins and centres the text at 80 characters wide (setting 40..400). `Esc` or the same key leaves. | F11 → only text and scrollbars |
| VEW-7 | Clickable links: URLs (`http`, `https`, `ftp`, `file`, `mailto`) are underlined and open with `Mod+click` (or double-click, setting) in the OS handler. Default on, off for large files. | `Mod+click` `https://example.org` → OS browser opens |
| VEW-8 | Base font, size, line spacing and caret width and blink rate are settings; caret position and scroll are never changed by changing them. | change font size → caret on same character |

## Out of scope

- Document map, minimap, function list panels; tab bar themes; toolbar customisation.

## Decisions

- Column in the status bar counts visual columns with tab expansion, not characters: it matches what an edge column at 80 shows.
- `Alt+Z` for word wrap, matching another common editor; the source has no default key.
