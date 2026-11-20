---
name: open
description: Open content in Worktray, the user's local scratchpad app with rich editors for Markdown, JSON, YAML, CSV, SVG, HTML, task boards and more. Use when the user asks to open, show, view or review something in Worktray, when their instructions (such as a CLAUDE.md) say to open certain kinds of output there, or to quietly show them a result worth looking at while you work (`--background`). Otherwise don't open Worktray unprompted; after writing a long standalone document (a plan, report, spec or dataset the user will want to read or edit outside the chat), you may offer once to open it there.
---

# Worktray

Worktray (https://worktray.io) is a local-first scratchpad. Its files are ordinary files on disk, grouped into **trays** — each desktop tray is a real folder — and each file opens in an editor chosen by its extension. Nothing the user opens or types leaves their machine.

## Before opening anything

Run `command -v worktray`. If it isn't found, skip to "Without the desktop command" below.

Then check the installed command knows `--view`: `grep -q -- --view "$(command -v worktray)"`. If it doesn't, the command predates Worktray 0.1.30. Leave `--view`/`--edit` out (an old command would treat `--view` as a file name), and mention once that reinstalling the command from Worktray's command palette adds it.

Check `--background` the same way before using it: `grep -q -- --background "$(command -v worktray)"`. Commands installed before Worktray 0.1.37 don't know it; leave it out and open normally.

## Choose how to open it

**Content you produced, for the user to read or tweak — the usual case.** Pipe it in:

```sh
worktray - --view dark-mode-plan.md <<'EOF'
# Dark-mode screenshots
...
EOF
```

- It becomes a new file in the user's **Inbox** tray (`~/Worktray/Inbox`), opens immediately, and the command prints the file's absolute path. Remember that path.
- The name is optional (default: a timestamped `.md`). A name without an extension gets `.md`. A taken name gets a `-2` suffix — nothing is overwritten. Only the base name is used; folders in the name are dropped.
- Use this for a **copy** of an existing file too, when the user only wants to read it: `worktray - --view notes.md < path/to/notes.md`.

**An existing file the user wants to edit in place**: `worktray path/to/file`. Know what this does before using it:

- Worktray opens **the file's whole folder as a tray** (or the user's existing tray for a folder above it) and selects the file. In a code repository that means the entire repo becomes a tray, which can take several seconds and adds a tray to the user's list. The first time a folder is opened, the user has to confirm.
- So use it only when the user wants to work on the real file, not just look at it — otherwise pipe a copy.

**A folder**: `worktray path/to/folder` opens it as a tray (same confirm on first open).

The command returns immediately; don't wait for it or poll for the window.

## Show without interrupting: `--background`

Add `--background` to any of the commands above (`worktray --background path/to/file`, `worktray - --background --view notes.md`) to open it exactly the same way, but without bringing Worktray to the front. The user keeps typing in the terminal and looks when they're ready. If Worktray wasn't running, it may still come to the front as it starts; that's expected.

- Use it while you work, to show the user something worth seeing: an HTML page you built, a diagram, a data file you generated, the plan you're about to follow. Then tell them in one line what you put in Worktray.
- This is the one case where you may open Worktray without being asked. Pick moments; don't run it after every edit. A few times in a task is plenty.
- Leave it off when the user asked to see something now. A normal open brings the window forward.
- The rules above still apply: an existing file opens its whole folder as a tray (the user confirms a new folder once), so for something they only need to read, pipe a copy.

## Choose the tab: `--view` or `--edit`

Most editors have two tabs: an editing tab showing the file's source, and a viewing tab (Markdown's rendered preview, a JSON tree, a CSV table and so on; see the table below). `--view` opens the file on its viewing tab and `--edit` on its editing tab. The flag works anywhere among the arguments: `worktray - --view plan.md`, `worktray --view path/to/file`.

- Pass `--view` when the user will mainly **read**: a plan, report, review or write-up in Markdown, or data to look over.
- Pass `--edit`, or nothing, when they'll **change** the text: a draft to tweak, a message to edit before pasting, a config.
- Without a flag, each editor opens on its usual tab, which for Markdown is the source.
- Editors with one view (plain text, code, images, PDFs) ignore it. It does nothing for folders.

## After opening

Edits in Worktray save straight to disk. If the user may have edited the file — the Inbox copy or a file opened in place — re-read it from its path before changing it, and never overwrite it from your own earlier version.

## Pick the extension for the editor

| Extension | Editor | `--view` shows |
| --- | --- | --- |
| `.md` | Markdown source with a rendered GitHub-Flavored preview | Preview |
| `.txt` | Plain text | — |
| `.json` | JSON with validation, pretty-print and a tree view | Tree |
| `.yaml` / `.yml` | YAML with formatting and a tree view | Tree |
| `.xml` | XML source and element tree | Tree |
| `.csv` / `.tsv` | Raw text or an interactive table | Table (default) |
| `.svg` | SVG source, or a visual canvas for drawing and arranging shapes | Design canvas (default) |
| `.html` | HTML source with a sandboxed live preview | Preview |
| `.js` / `.ts` | Code the user can run in an isolated console | — |
| `.css` | CSS | — |
| `.sql` | SQL formatting only (no database connection) | — |
| `.env` | Key/value form or raw text | Form (default) |
| `.jwt` | Decodes a JSON Web Token locally (the file holds just the token) | Decoded |
| `.curl` | A single `curl …` command, shown as parsed fields; the Mac app can send it | Fields |
| `.openapi` | An OpenAPI/Swagger spec (YAML or JSON) as searchable endpoint cards | Explore (default) |
| `.task` | One ticket: `{"title": "…", "description": "…", "status": "pending"}` — status is `pending`, `in-progress`, `complete` or `cancelled` | Ticket (default) |
| `.taskboard` | A board, `{"name": "…"}`, showing the `.task` files **in the same folder** as columns by status | — |
| `.png` `.jpg` `.webp` `.bmp` / `.pdf` / `.wav` `.mp3` `.m4a` `.ogg` `.flac` | Images (simple edits), a PDF reader, and an audio player with waveform and trimming — open existing files; don't pipe binary data | — |

Any other extension is kept, but opens as a placeholder rather than an editor — use `.txt` for plain text such as logs.

**Several related files** (for example a `.taskboard` and its `.task` tickets): `worktray -` puts every file loose in the Inbox, and a board there would gather every ticket in the Inbox. Instead, write the files into their own folder — for example `~/Worktray/<project-name>/` — and open that folder with `worktray ~/Worktray/<project-name>` (the user confirms once).

## Without the desktop command

The web app can import one file from a link. The content travels in the URL fragment (after `#`), which browsers never send to a server, but it stays in browser history — so only use this for small, non-sensitive text, and say so:

```sh
python3 -c 'import sys, urllib.parse, webbrowser; webbrowser.open("https://worktray.io/#new=" + urllib.parse.quote(sys.argv[1]) + "&content=" + urllib.parse.quote(sys.stdin.read()))' notes.md < notes.md
```

Worktray asks the user before adding the file to their current tray. If neither route fits, tell the user the desktop app's `worktray` command can be installed from Worktray's command palette (Cmd+K, then type `>`).
