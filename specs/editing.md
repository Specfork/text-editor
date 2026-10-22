# Editing

**Status:** Draft · **Prefix:** EDT

## Purpose

Changing text fast: undo, several carets, rectangular selections, number columns, line and case operations, indentation, comments and bookmarks.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| EDT-1 | Undo `Mod+Z`, redo `Mod+Y` and `Mod+Shift+Z`, limited only by memory. Consecutive typed characters group into one step until the caret moves or 1 s passes. Saving keeps the history; undoing back to the saved state clears the modified marker. | type, save, type, undo → not modified |
| EDT-2 | Multiple carets: `Mod+click` adds a caret (setting, default on); `Mod+D` adds the next occurrence of the selection (or of the word at the caret) as a new selection; `Esc` returns to one caret. Typing, deleting, moving and selecting apply at every caret. | 3 carets, type `x` → 3 `x` inserted |
| EDT-3 | Paste with N carets when the clipboard holds exactly N lines puts one line at each caret, in caret order; otherwise the whole clipboard at each caret. | 3 carets, paste `a\nb\nc` → a, b, c |
| EDT-4 | Rectangular selection: `Alt+drag` or `Alt+Shift+arrows`. Typing or pasting replaces the block on every line; past a line's end, spaces are added as needed (virtual space). Copying a block puts one line per row on the clipboard and marks it rectangular, so pasting re-creates a block. | select 3-line block, type `#` → `#` on 3 lines at same column |
| EDT-5 | Column editor (on a rectangular selection or the caret column through the following lines): insert text, or numbers with `initial`, `step`, `repeat` (each value N times), `base` (`dec`, `hex`, `oct`, `bin`) and `pad` (`none`, `zeros`, `spaces`) to the widest value. | 5 lines, initial 8, step 1, pad zeros → `08`..`12` |
| EDT-6 | Line commands on the selected lines or the caret line: duplicate `Mod+Shift+D`, delete `Mod+Shift+K`, move up `Alt+Up`, move down `Alt+Down`, join (with one space), split at the edge column. | caret line 3, `Alt+Up` → becomes line 2 |
| EDT-7 | Sort lines (selection or whole document): ascending or descending; `text` (code-point order), `text_ignore_case`, `locale`, `integer`, `decimal` (both use the first number on the line; lines without one sort first); stable. | sort integer `10, 9, a` → `a, 9, 10` |
| EDT-8 | Clean-up commands: remove duplicate lines (all or consecutive only, keeping the first), remove empty lines (or also whitespace-only), trim trailing, trim leading, trim both, tabs→spaces and spaces→tabs (leading only or all) using the tab width. Each is one undo step. | `a, b, a` remove duplicates → `a, b` |
| EDT-9 | Case: upper `Mod+Shift+U`, lower `Mod+U`, title case, sentence case, invert; Unicode-aware. | `ǆemal` title → `ǅemal` |
| EDT-10 | Indentation per language: `tab_width` (default 4, 1..16) and `use_spaces` (default off). Enter keeps the previous line's indentation (setting, default on). `Tab`/`Shift+Tab` on a multi-line selection indents or outdents every selected line. | select 3 lines, `Tab` → each starts with one tab |
| EDT-11 | Comments from the language definition ([LNG-1](languages.md)): toggle line comment `Mod+/` (adds when any selected line lacks it, else removes); block comment `Mod+Shift+/` wraps the selection; languages without the needed form disable the command. | toggle on 2 Python lines → both start with `# ` |
| EDT-12 | Matching pairs: the bracket at or before the caret and its partner are highlighted (default on); `Mod+B` jumps to the partner. Auto-closing of `( [ { " '` (each optional, default off) inserts the closer and types over it when typed next. | caret after `(` → its `)` highlighted |
| EDT-13 | Word completion: after `completion_min_chars` (default 3, 1..9) word characters, a list offers matching words from the document and the language's keywords, sorted alphabetically; `Enter` or `Tab` accepts, `Esc` closes. Setting off by default for large files ([FIL-12](files.md)). | type `fun` in a JS file → `function` offered |
| EDT-14 | Bookmarks: toggle `Mod+F2`, next `F2`, previous `Shift+F2`, clear all; shown in a margin; they move with their lines and are removed with them. Commands to cut, copy, delete or keep only bookmarked lines. | bookmark line 5, insert 2 lines above → bookmark on line 7 |
| EDT-15 | Go to line `Mod+G`: a line number, or a character offset when chosen; out-of-range values go to the last line. Overwrite mode toggles with `Insert`. | `Mod+G 9999` in a 100-line file → line 100 |

## Out of scope

- Code intelligence: symbol lists, go to definition, call tips, linting.
- Spell checking; snippets.

## Decisions

- `Mod+D` adds the next occurrence and duplicate moves to `Mod+Shift+D`: the multi-caret binding is what users of current editors expect; the source uses other keys.
- Delete line is `Mod+Shift+K` to avoid clashing with select-all-occurrence bindings.
