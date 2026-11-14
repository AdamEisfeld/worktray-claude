# Worktray for Claude Code

A [Claude Code](https://claude.com/claude-code) plugin that lets Claude sessions open their output — plans, reports, Markdown, JSON, YAML, CSV, SVG, HTML, task boards and more — in [Worktray](https://worktray.io), a local-first developer scratchpad, so you can read and edit it comfortably outside the chat.

## Install

In Claude Code:

```
/plugin marketplace add AdamEisfeld/worktray-claude
/plugin install worktray@worktray
```

For the best experience, install the [Worktray desktop app for Mac](https://worktray.io) and its `worktray` command (in Worktray: **Cmd+K**, type `>`, choose **Install the “worktray” command line tool**). Without the desktop command, the skill falls back to opening small snippets in the Worktray web app through a link whose content never leaves your browser.

## Use

The plugin adds one skill, `worktray:open`. Ask Claude to "open that in Worktray". By default the skill only opens Worktray when you ask; to have Claude open certain kinds of output every time, add an instruction to your `CLAUDE.md` — see [the Worktray docs](https://docs.worktray.io/opening-content/#let-claude-code-open-things-in-worktray).

## Updating

```
/plugin marketplace update worktray
```

The skill in this repository is published from the Worktray app's source with each Worktray release.
