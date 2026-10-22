---
specfork: 1
name: "@specfork/text-editor"
summary: Desktop text and source code editor. Tabs, encodings and line endings, large files, find and replace with regex and find in files, syntax highlighting, multi-cursor, macros, session restore.
license: CC-BY-4.0
derived_from:
  repo: https://github.com/notepad-plus-plus/notepad-plus-plus
  commit: 6abb408462b135a549686d989e1d5db231d31951
  url: https://github.com/notepad-plus-plus/notepad-plus-plus
  license: GPL-3.0-or-later
---

# Text editor

One person edits plain text and source code files on their own computer. Files open in tabs,
keep their encoding and line endings byte for byte, and can be converted on request. Find and
replace works in one file, all open files, or a folder tree, with regular expressions. Languages
are described by data files that drive highlighting, comments and folding. Several carets and
rectangular selections edit many lines at once, macros repeat edits, and the whole session,
unsaved text included, comes back after a restart or crash.
Behaviour derived from Notepad++'s open-source code (GPL-3.0-or-later), written fresh, stack-free
and for any desktop OS.
Not affiliated with or endorsed by Don Ho or the Notepad++ project. The name is used only to credit the source.

| Area | Prefix | Covers |
|---|---|---|
| [documents.md](specs/documents.md) | DOC | Tabs, untitled documents, open and close, recent files, split view, command line |
| [files.md](specs/files.md) | FIL | Encodings, detection, conversion, line endings, large and binary files, external changes |
| [editing.md](specs/editing.md) | EDT | Undo, multi-cursor, column editing, line and case operations, indentation, bookmarks |
| [search.md](specs/search.md) | FND | Find, replace, extended escapes, regex, mark, find in files, results panel |
| [languages.md](specs/languages.md) | LNG | Language-definition contract, detection, themes, folding, comments |
| [macros.md](specs/macros.md) | MAC | Record, replay, repeat, save macros |
| [session.md](specs/session.md) | SES | Session restore, unsaved buffers, periodic snapshots, backups on save |
| [view.md](specs/view.md) | VEW | Zoom, word wrap, whitespace, margins, status bar |
| [commands.md](specs/commands.md) | CMD | Command list, shortcut mapping, minimal plugin command interface |

Single user, no account, no network: everything runs on one computer (Windows, macOS, Linux).
Shortcuts are defaults and can be remapped ([CMD-2](specs/commands.md)); `Mod` = Ctrl on Windows
and Linux, Cmd on macOS; `Alt` = Option on macOS. Line and column numbers shown to the user start at 1.
