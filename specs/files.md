# Files

**Status:** Draft · **Prefix:** FIL

## Purpose

Read and write files exactly: encodings, byte order marks, line endings, detection, explicit conversion, very large or binary files, and changes made by other programs.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| FIL-1 | Encodings: `utf-8` (no BOM), `utf-8-bom`, `utf-16le` and `utf-16be` (with BOM; also read without), and legacy code pages, at least windows-1250..1258, ISO-8859-1..16 (not 12), KOI8-R, KOI8-U, IBM437, IBM850, Mac Roman, Shift_JIS, EUC-JP, GBK, GB18030, Big5, EUC-KR, TIS-620. Names follow the WHATWG/IANA labels. | open each test file → text shown correctly |
| FIL-2 | Round trip: opening and saving without edits writes the same bytes: same encoding, BOM, line endings (mixed ones too) and trailing newline or none. | open + save 50 sample files → all byte-identical |
| FIL-3 | Detection order on open: BOM (UTF-8, UTF-16 LE/BE) → UTF-16 without BOM when, in the first 4 KiB, ≥ 40 % of bytes at one parity are 0x00 and none at the other → an encoding declared in the first 1 KiB (`<?xml … encoding=…?>`, `<meta charset=…>`) → valid UTF-8 (pure ASCII counts) → statistical guess (setting, default on) → the `fallback_encoding` setting (default windows-1252). | UTF-8 file without BOM containing `é` → `utf-8` |
| FIL-4 | Bytes invalid in the document's encoding are shown as visible hex markers (e.g. `xE9`), kept byte for byte when saving, and counted in the status bar tooltip. | UTF-8 file with one stray `0xE9` → marker shown, saved unchanged |
| FIL-5 | "Reinterpret as <encoding>" reloads the file bytes with that encoding, changes nothing on disk and does not mark the document modified; on a modified document it first asks to discard unsaved changes. | reinterpret windows-1251 file as KOI8-R → text changes, not modified |
| FIL-6 | "Convert to <encoding>" keeps the text, changes the encoding used on the next save and marks the document modified. Characters the target cannot represent are listed (first 20 with line:column) and the conversion asks to proceed (they become `?`) or cancel. | convert `€` text to ISO-8859-1 → warning names line:column |
| FIL-7 | New documents use `default_encoding` (default `utf-8`). A setting also applies it to opened pure-ASCII files (default on), so typed non-ASCII text saves as UTF-8. | new doc, type `ü`, save → bytes `C3 BC` |
| FIL-8 | Line endings `crlf`, `lf`, `cr`. A document's type = the first line ending in the file; a file with none uses `default_eol` (default: CRLF on Windows, LF elsewhere). Enter inserts the document's type; pasted text is normalised to it (setting, default on). | LF file, press Enter → `0A` inserted |
| FIL-9 | "Convert line endings to <type>" rewrites every line ending, mixed ones included, as one undo step, and marks the document modified. | file with CRLF and LF lines → all LF after convert |
| FIL-10 | Saving (`Mod+S`) writes to a temporary file in the same folder and replaces the original, keeping its permissions; where that is not possible (no rights to the folder) it writes in place. Failure leaves the old file intact and the document modified. "Save as" `Mod+Alt+S`, "save a copy as", "save all" `Mod+Shift+S`. | disk full during save → original unchanged |
| FIL-11 | Saving to a location that needs higher OS rights tells the user and offers "save as"; the editor never elevates itself silently. | save to a protected folder → message, no write |
| FIL-12 | Large files: at or above `large_file_size` (default 200 MiB, settable 1..4096 MiB) the document opens without syntax styling, word wrap, matching-pair highlight, auto-completion, occurrence highlight or clickable links, each re-enableable per setting. Opening shows progress with cancel. Files over 2 GiB ask to confirm first. | open 300 MiB log → opens plain, scrolling stays smooth |
| FIL-13 | A file with a 0x00 byte in its first 8 KiB that is not UTF-16 opens as text with visible markers for control bytes and saves byte for byte. | open small binary, save → identical |
| FIL-14 | External changes: on window focus and through a file watcher, each open file is checked (size, modification time). Setting `on_external_change`: `ask` (default), `reload` (silent when the document has no unsaved changes, else ask), `off`; plus `follow_end` (after reload, scroll to the end). The prompt appears when that document is active. | `ask`, another app appends a line → prompt on focus; yes → line shown |
| FIL-15 | Reload keeps caret line, scroll position and bookmarks where those lines still exist, and adds an undo point so the reload can be undone. | reload after append, `Mod+Z` → previous text |
| FIL-16 | A file deleted or moved by another program: the prompt offers keep (document stays open, marked modified so it can be saved again) or close. | delete open file outside → prompt; keep → modified marker |

## Out of scope

- Remote files (SFTP, cloud); file locking against other programs.
- Hex editing view.

## Decisions

- Pure-ASCII files are treated as UTF-8 by default (the source treats them as the system code page): typed non-ASCII text then saves portably on every OS.
- Fallback legacy code page is a setting instead of the OS code page: macOS and Linux have no system "ANSI" code page.
- Mixed line endings are preserved until the user converts; the document type is only for new lines.
