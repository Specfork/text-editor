# Commands

**Status:** Draft · **Prefix:** CMD

## Purpose

Every action is a named command with a remappable shortcut, and add-ons can contribute commands through one small interface.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| CMD-1 | Every menu action is a command `{id (dotted, stable, e.g. "file.save"), title, category, default_shortcut?}`. Ids never change between versions; renamed actions keep their id. | menu "Save" → command `file.save` |
| CMD-2 | Shortcut mapping lists every command, macro and add-on command with its shortcut; the user can assign, clear, or reset one or all. Assigning a shortcut already used asks to move it (the other loses it) or cancel. Saved in the user config folder as JSON `{id: shortcut}` with only differences from defaults. | assign `Mod+S` to another command → conflict prompt |
| CMD-3 | Shortcut syntax: modifiers `Mod`, `Ctrl`, `Alt`, `Shift`, `Meta` joined by `+` with one key; two-step chords are not supported. On macOS `Mod` = Cmd and `Ctrl` is the Control key. | `Mod+Shift+K` on macOS → Cmd+Shift+K |
| CMD-4 | Settings live in one JSON file in the user config folder (per OS convention); portable mode: when a marker file `portable` sits beside the app, config, sessions, snapshots and macros live beside the app instead. | marker present → no files written to the user config folder |
| CMD-5 | Add-on: a folder in the add-ons folder with `manifest.json` `{id, name, version, api_version, entry, commands: [{id, title, shortcut?}]}`. Its commands appear in an add-ons menu under its name and in the shortcut mapping as `<addon id>.<command id>`. | install add-on with 2 commands → 2 menu items |
| CMD-6 | Add-on interface (`api_version` 1): on its command, an add-on can read the active document's text, path, language, encoding, line ending, selections and carets; replace a range or the selections (one undo step per call); open a file; create a new document with text; show a message. Nothing else of the editor is reachable. | add-on uppercases selection → one undo restores |
| CMD-7 | Add-on events (subscribe in the manifest): `document_opened`, `document_activated`, `document_saved`, `document_closed`, each with the document's path or untitled name. | subscribe `document_saved`, save → add-on receives path |
| CMD-8 | Load safety: an add-on with an unsupported `api_version`, invalid manifest or failing load is skipped with a message naming it; an add-on error during a command is reported and the document is left as before that call. `--no-plugins` disables all add-ons. | manifest api_version 9 → skipped with message |

## Out of scope

- Add-on discovery, download, update and signing; add-on UI panels, docking windows and custom lexers.
- The source's native plugin binary interface and its message list.
- Command palette (implementations may add one from CMD-1).

## Decisions

- Add-ons get a narrow, versioned document API instead of full editor access: enough for text transforms, safe to keep stable across stacks.
