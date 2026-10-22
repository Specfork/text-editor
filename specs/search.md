# Search

**Status:** Draft · **Prefix:** FND

## Purpose

Find and replace in the current document, in all open documents, or across a folder tree, as plain text, with escape sequences, or with regular expressions.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| FND-1 | Find `Mod+F`, replace `Mod+H` (`Mod+Alt+F` on macOS), find in files `Mod+Shift+F`; next `F3`/`Enter`, previous `Shift+F3`. The search field starts with the selection (if on one line) or the word at the caret. The last 10 search and replace strings are remembered across restarts. | select `foo`, `Mod+F` → field shows `foo` |
| FND-2 | Modes: `normal` (literal), `extended`, `regex`. Options: `match_case` (default off), `whole_word` (not in regex), `wrap` (default on), direction up/down, `in_selection`, and in regex `dot_matches_newline` (default off). | whole word `cat` in `concat cat` → second only |
| FND-3 | Extended mode escapes in both fields: `\n` `\r` `\t` `\0` `\\`, `\xHH`, `\uHHHH`, `\oOOO` (octal), `\dDDD` (decimal), `\bBBBBBBBB` (binary byte). An invalid escape is taken literally. | find `\t` extended → matches a tab |
| FND-4 | Regex flavour: Perl-compatible on Unicode text: classes, `\d \w \s \b`, Unicode properties `\p{L}`, lazy and possessive quantifiers, numbered and named groups, backreferences, lookahead and lookbehind, `^`/`$` at line starts and ends, `\A` `\z` document bounds, `\R` any line break; matches may span lines. Invalid pattern → message with the error position; nothing replaced. | `(?<y>\d{4})-\d\d` finds `2024-05` |
| FND-5 | Regex replacement: `$0`/`$&`, `$1`..`$99`, `${name}`, `\1`..`\9`; case changes `\U` `\L` (until `\E`), `\u` `\l` (next char); `\n` `\t` escapes; `$$` for a literal `$`. | `(\w+)` → `\U$1` on `ab` → `AB` |
| FND-6 | An empty match moves the search one character on so it never loops; "replace all" with an empty-matching pattern inserts once per position. | regex `^` replace all with `> ` → every line prefixed once |
| FND-7 | Replace all in the document (or selection) is one undo step and reports the count; "replace all in all open documents" reports per document and is one undo step per document. | replace all 12 → status "12 replaced"; undo → all back |
| FND-8 | Count reports the number of matches without moving the caret. Mark highlights every match with a mark style, optionally bookmarks their lines, optionally clearing previous marks first; "clear marks" removes them. | mark `TODO` with bookmark → each line bookmarked |
| FND-9 | Find all in the current document or all open documents → results panel: grouped by document, each hit as `line: text` with matches highlighted, totals per document and overall; activating a hit opens the document and selects the match. | find all `x` in 2 docs → 2 groups with counts |
| FND-10 | Find in files: folder (default the active document's folder), filters (default `*.*`; space- or `;`-separated globs; `!glob` excludes files; `!\name` excludes folders), include subfolders (default on), include hidden (default off). Files with a 0x00 byte in their first 8 KiB (not UTF-16) are skipped. | filters `*.c !test_*.c` → test files not searched |
| FND-11 | Find in files runs in the background with progress (files done/total) and cancel; results stream into the results panel in folder order; each file is read with detection from [FIL-3](files.md). | cancel after 1 s → partial results kept, marked cancelled |
| FND-12 | Replace in files asks to confirm with the folder and filters, then writes each changed file keeping its encoding, BOM and line endings; files open with unsaved changes are changed in their buffer instead and left modified; read-only or failing files are listed and skipped. Reports files changed and total replacements. | 3 files, 1 read-only → 2 changed, 1 listed |
| FND-13 | Results panel: one search per block, newest on top, collapsible; lines longer than 1024 characters show a window around the match; copy selected result lines; `F4`/`Shift+F4` go to next/previous hit. | `F4` → next hit selected in its document |
| FND-14 | Occurrence highlight: selecting a whole word highlights its other occurrences in the visible area (setting, default on; options match case and whole word). | select `count` → other `count` highlighted |
| FND-15 | No match → message in the find window or status bar, no dialog; with `wrap` on, a search passing the document end continues from the start and says so. | wrap, search past end → "wrapped" shown, next match selected |

## Out of scope

- Search across documents' history; fuzzy search; multi-line find field editor beyond escapes and regex.

## Decisions

- Binary files are skipped in find in files: the source searches them, which mostly produces noise and risks corrupting them on replace.
- One regex flavour for every OS: results must not depend on the platform's library.
