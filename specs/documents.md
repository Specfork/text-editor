# Documents

**Status:** Draft · **Prefix:** DOC

## Purpose

Open documents as tabs: creating, opening, switching, closing and reopening them, in one or two side-by-side views.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| DOC-1 | Each open document is one tab: a file path or an untitled buffer. A new untitled document (`Mod+N`, or double-click on empty tab bar space) is named `new <n>` with the lowest free n ≥ 1; it can be renamed without saving. | close `new 1` with `new 2` open, `Mod+N` → `new 1` |
| DOC-2 | Tab shows the file name, a modified marker while unsaved, a read-only marker, and the full path as tooltip. Middle-click or the tab's close button closes it. Tabs reorder by drag. | edit → marker shown; save → marker gone |
| DOC-3 | Open (`Mod+O`, multi-select, drag and drop of files onto the window, or command line) a file already open activates its tab instead of opening it again; paths compare case-insensitively on case-insensitive file systems. | open same file twice → one tab |
| DOC-4 | Opening a file while the only tab is an empty, unmodified untitled document replaces that tab. | fresh start, open file → 1 tab |
| DOC-5 | Closing (`Mod+W`) a modified document asks save / don't save / cancel; "close all", "close all but this", "close all to the left/right" and "close all unchanged" ask once per modified document, with "save all" and "discard all" choices. Closing the last tab leaves one empty untitled document. | close all with 3 modified → prompts offer "save all" |
| DOC-6 | Reopen closed document `Mod+Shift+T` reopens the most recently closed file at its last caret position, repeatable through the last 20 closed. Recent files list: 10 entries by default (0..30), newest first, missing files removed when chosen with a message. | close 2 files, `Mod+Shift+T` twice → both back |
| DOC-7 | Switch documents: `Ctrl+Tab`/`Ctrl+Shift+Tab` in most-recently-used order (setting, default on) or tab order, `Ctrl+PgDn`/`Ctrl+PgUp` in tab order. | MRU on: A, B, C used in that order, Ctrl+Tab from C → B |
| DOC-8 | Read-only: a file the OS marks read-only, or a document toggled read-only by the user, refuses edits with a short notice; saving such a file asks for "save as". | toggle read-only, type → text unchanged |
| DOC-9 | Two views: a document can be moved or cloned to the second view (side by side or stacked, setting). A clone shares the same buffer: edits appear in both, caret and scroll are separate. Optional synchronised vertical and horizontal scrolling. | clone, type in view 1 → visible in view 2 |
| DOC-10 | Single instance by default: launching again forwards its files and options to the running instance and exits; `--new-instance` starts another one. | open file from file manager → opens as a tab in the running window |
| DOC-11 | Command line: `[files…] --line <n> --column <n> --language <id> --read-only --no-session --new-instance --no-plugins`; `--line`/`--column` place the caret in each given file; unknown options are reported and ignored. | `--line 120 big.txt` → caret on line 120 |
| DOC-12 | Document actions: copy full path, copy file name, open containing folder in the OS file manager, rename file on disk (renames an open file and keeps its tab), delete file (to the OS trash after confirmation). | rename `a.txt` → `b.txt` → tab shows `b.txt`, undo history kept |

## Out of scope

- Project or workspace panels, folder tree side panel, document list panel.
- Printing and print preview.

## Decisions

- File deletion only to the OS trash: never a permanent delete from the editor.
- MRU switching default on: it is the common desktop behaviour on all three OSs.
