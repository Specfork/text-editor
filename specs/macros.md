# Macros

**Status:** Draft · **Prefix:** MAC

## Purpose

Record a sequence of edits once and replay it, a set number of times or down to the end of the document, and keep useful macros with names and shortcuts.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| MAC-1 | Start and stop recording `Mod+Shift+R`; a recording indicator shows while active. Recording a new macro replaces the current unsaved one. | record → indicator; stop → indicator gone |
| MAC-2 | Recorded steps: typed text, caret moves and selections by keyboard, editing commands ([EDT-6](editing.md)..[EDT-9](editing.md), clipboard, indent, comment), and find or replace runs with all their options. Mouse clicks, dialogs that open files, and settings changes are not recorded; the user is told when recording stops which were skipped. | record with a mouse click → "1 action not recorded" |
| MAC-3 | Play `Mod+Shift+P` runs the current macro once at the active caret. | record `Home`, `#`, `Down`; play → next line commented |
| MAC-4 | Run multiple: N times (1..100000) or until the end of the document; "until end" stops when a run reaches the last line, when the caret did not advance during a run, or when a recorded find finds nothing. | 10-line file, macro edits and moves down → stops after line 10 |
| MAC-5 | A whole playback (all repetitions) is one undo step. | run 50 times, `Mod+Z` once → document as before |
| MAC-6 | Save current macro with a name (unique, 1..64 chars) and optional shortcut ([CMD-2](commands.md) conflict rules); saved macros appear in a macro menu, survive restarts, and can be renamed, re-bound or deleted. | save "prefix", restart → listed and runnable |
| MAC-7 | Macro file (JSON, in the user config folder): `[{name, shortcut?, steps: [{type: "command", id} \| {type: "text", value} \| {type: "find", mode, find, replace?, options, action: "next"\|"replace"\|"replace_all"}]}]`. Unknown step types make that macro unavailable with a message, others still load. | hand-edit a bad step → that macro disabled, others work |
| MAC-8 | Playback with multiple carets or a rectangular selection applies each step at every caret as typing would ([EDT-2](editing.md)). | 3 carets, play `End`, `;` → 3 lines end in `;` |

## Out of scope

- A scripting language; editing macro steps inside the app (the file can be edited).

## Decisions

- One undo step per playback: replay is a single user intention; the source undoes step by step.
