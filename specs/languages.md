# Languages

**Status:** Draft · **Prefix:** LNG

## Purpose

Languages are data: a language-definition file says how to colour, comment and fold a kind of file, and a theme says how each kind of token looks.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| LNG-1 | Language definition (JSON): `{id, name, extensions[], first_line[] (regex), case_sensitive, line_comment, block_comment {open, close}, keyword_groups (≤ 8 lists of words), operators[], delimiters (≤ 8: {open, close, escape}), numbers {prefixes[], suffixes[], extra_chars[], range_separator}, folds {code: [{open, middle?, close}] (≤ 3), comment: [{open, close}]}}`. Every field but `id` and `name` is optional. | load a minimal `{id, name}` → valid, everything plain |
| LNG-2 | Tokens the highlighter emits: `default`, `comment`, `comment_line`, `number`, `keyword1`..`keyword8`, `operator`, `delimiter1`..`delimiter8`, `fold_open`, `fold_close`. Built-in languages may add their own token classes, each falling back to one of these for themes that do not name it. | theme without `string` → strings shown as `delimiter1` |
| LNG-3 | Matching rules: keywords match whole words, ignoring case unless `case_sensitive`; a keyword ending in `*` matches any word starting with it; delimiters span lines until their close and honour their escape character; comments and delimiters win over keywords inside them. | `"a\"b"` with escape `\` → one delimiter token |
| LNG-4 | The editor ships definitions for at least: plain text, C, C++, C#, Java, JavaScript, TypeScript, JSON, Python, Ruby, PHP, Go, Rust, Shell, PowerShell, Batch, SQL, HTML, XML, CSS, Markdown, YAML, INI, Makefile, Lua, Perl, Diff. Users add or override definitions by dropping files into a user folder; a user file with a built-in `id` replaces it. | user file `id: python` → used instead of built-in |
| LNG-5 | Language of a document, first match: user choice for that document (menu or `--language`) → extension (case-insensitive; user definitions before built-ins) → first line: `#!` interpreter name (`python3` → python), `<?xml` → xml, `<!DOCTYPE html`/`<html` → html, `<?php` → php, then each definition's `first_line` patterns → plain text. Saving with a new extension re-detects unless the user chose. | `script` with `#!/usr/bin/env bash` → shell |
| LNG-6 | Theme (JSON): `{name, dark: bool, default: style, styles: {token: style}, ui: {caret, selection, current_line, margin, fold_margin, whitespace, edge, marks}}` where style = `{fg, bg, bold, italic, underline, font?, size?}` with colours `#RRGGBB`; missing fields inherit from `default`. Ships a light and a dark theme; the default follows the OS appearance. | OS switches to dark → dark theme applied |
| LNG-7 | Highlighting is incremental: the visible range is styled first; after an edit only from the edited line onwards until the styling state matches the previous run; typing in a 10 MB file stays under 16 ms per keystroke. | type in 10 MB C file → no visible lag |
| LNG-8 | Folding: a margin shows fold points from `folds` (and from indentation for languages that set `fold_by_indent`); fold/unfold current `Mod+Alt+[`/`Mod+Alt+]`, fold all `Alt+0`, unfold all `Alt+Shift+0`, fold level n `Alt+<n>` (1..8). Editing inside a folded block unfolds it. | fold all in a 3-function file → 3 folded blocks |
| LNG-9 | A language editor lets users create, copy, rename, delete, import and export definitions (LNG-1 files) and preview them on sample text; export then import gives an identical definition. | export, delete, import → identical JSON |
| LNG-10 | A broken definition or theme file (invalid JSON, unknown field type, > 8 groups) is skipped with a message naming the file and problem; the rest load. | malformed file → message, other languages fine |

## Out of scope

- Language servers, semantic highlighting, code outline panel.
- The source's per-language lexer internals; grammar formats from other editors.

## Decisions

- One JSON contract for user and built-in languages: the source has a compiled lexer per language and a separate user format; here built-ins may still use richer engines but must expose the same token names.
- Theme follows OS appearance by default on all three OSs.
