# Session

**Status:** Draft · **Prefix:** SES

## Purpose

Quit and come back to exactly where you were, unsaved text included, even after a crash, and keep optional copies of files before they are overwritten.

## Requirements

| ID | Requirement | Verified by |
|---|---|---|
| SES-1 | With `remember_session` (default on), quitting stores per view: open documents in tab order, the active one, and per document: path or untitled name, caret and selections, first visible line, folded lines, bookmarks, chosen language and encoding (when user-set), read-only flag and zoom. Starting restores it. | 5 tabs, caret line 40 in tab 3, quit, start → same tabs, caret line 40 |
| SES-2 | Files given on the command line open in addition to the session and become active; `--no-session` opens only them. Files that no longer exist are skipped, and a message lists them. | delete one session file, start → message names it |
| SES-3 | Unsaved buffers (`snapshot`, default on): every `snapshot_seconds` (default 7, 1..3600) each modified or untitled document whose text changed since its last snapshot is written to the app's backup folder. Quitting does not ask to save; the next start restores those documents with their unsaved text, still marked modified, and untitled ones with their names. | type in `new 1`, quit, start → text back, modified |
| SES-4 | After a crash or forced end, the next start restores from the last snapshots (SES-3), so at most `snapshot_seconds` of typing is lost. | kill process 10 s after typing → text restored |
| SES-5 | A file changed on disk while its unsaved snapshot exists: on restore the snapshot text is shown, marked modified, and the external-change prompt ([FIL-14](files.md)) offers reloading the disk version. | edit file on disk between sessions → restored snapshot + prompt |
| SES-6 | A snapshot is deleted when its document is saved, closed with "don't save", or becomes unmodified through undo; the backup folder holds nothing else. Snapshots are plain files readable only by the current user. | save → snapshot file gone |
| SES-7 | Turning `snapshot` off: quitting asks save / don't save / cancel for each modified document again ([DOC-5](documents.md)). | setting off, quit with edits → prompt |
| SES-8 | Backup on save, setting `backup`: `none` (default), `simple` (copy the old file to `<name>.bak` next to it, overwriting), `timestamped` (copy into the backup folder or a chosen folder as `<name>.<YYYY-MM-DD_HHmmss>.bak`). A failed backup aborts the save with a message. | `simple`, save twice → one `.bak` with the version before the last save |
| SES-9 | Named sessions: save the current set of documents as a session file (JSON, same data as SES-1 without unsaved text) and load one later, replacing or adding to open documents (choice). | save session, close all, load → same tabs |

## Out of scope

- Syncing sessions between computers; per-project sessions tied to folders.

## Decisions

- Snapshot on by default: losing typed text is worse than a quit that does not ask.
- Snapshots are separate from backup-on-save: one protects unsaved text, the other protects the previous saved file.
