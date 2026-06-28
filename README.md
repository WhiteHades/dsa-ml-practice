# dsa-ml-practice

Terminal-first LeetCode practice with a ready-made tmux workspace.

The goal is simple: open one command, browse problems, edit in Neovim,
test, submit, and come back later without rebuilding the layout by hand.

```text
+-------------+---------------------------+
|  LEFT 35%   |  TOP-RIGHT 65%            |
|  leetcode   |  nvim                     |
|  TUI        |  solution editor          |
|             +---------------------------+
|             |  BOTTOM-RIGHT             |
|             |  shell for test/submit    |
+-------------+---------------------------+
```

## Quick Start

First-time setup:

```bash
cd ~/Codes/dsa-ml-practice
make setup
make login
make tmux
```

Daily use after setup:

```bash
cd ~/Codes/dsa-ml-practice
make tmux
```

`make tmux` is the recommended entry point. It creates or repairs the
`dsa-ml-practice` tmux session, then attaches to it. If you run it from
inside another tmux session, it switches clients instead of nesting tmux.

## Daily Workflow

1. Use the left pane to browse LeetCode problems.
2. Press `p` on a problem to pick it.
3. The solution file opens automatically in the top-right Neovim pane.
4. Write your solution and save it.
5. Press `C-a T` to run tests in the bottom-right pane.
6. Press `C-a S` to submit the current solution.
7. Press `C-a d` to detach when you are done.
8. Run `make tmux` later to resume.

## Key Bindings

These are the important tmux bindings for this workflow:

| binding | action |
| --- | --- |
| `C-a T` | test the current solution file |
| `C-a S` | submit the current solution file |
| `C-a c` | create a new tmux window with the same 3-pane layout |
| `C-a n` / `C-a p` | move to next / previous tmux window |
| `C-a w` | choose a tmux window from a list |
| `C-a d` | detach from tmux |
| `C-a C-s` | manually save tmux session state |
| `C-a r` | reload tmux config |

`C-a T` and `C-a S` are guarded so they only act inside the
`dsa-ml-practice` tmux session. They should not send LeetCode commands
into unrelated tmux sessions.

## Common Questions

**I solved a problem yesterday. Today I want a new approach. What do I do?**

Go to the problem in the left pane and press `p` again. The old current
solution is archived as a numbered file like `1.two-sum.1.py`, and a
fresh unnumbered file like `1.two-sum.py` opens in Neovim.

**Which file is the active attempt?**

The unnumbered file is always the active attempt. Numbered files are old
attempts kept for reference.

**Can I solve multiple problems at the same time?**

Yes. Press `C-a c` to create another tmux window. Each window gets its
own left TUI pane, Neovim pane, and test/submit shell pane.

**Are those windows saved after shutdown?**

tmux-continuum and tmux-resurrect are enabled in the dotfiles. For best
results, press `C-a C-s` before shutdown, then run `make tmux` after
booting again.

**Can I attach without `make tmux`?**

Yes, but it is not the recommended path. `tmux attach -t dsa-ml-practice`
can attach to an existing session, but it skips the repair step. Use
`make tmux` when you want the plug-and-play behavior.

## Full Guide

Read the detailed workflow guide here:

- [docs/workflow-compendium.md](docs/workflow-compendium.md)

It covers repeated attempts, next-day sessions, multiple windows, tmux
session safety, troubleshooting, generated files, snapshots, and the
recommended habits for this repo.

## Make Targets

```text
make help      show commands
make setup     install the LeetCode CLI fork and configure the workspace
make install   build/link the LeetCode CLI fork into PATH
make config    point the CLI workspace at this repo
make login     log in to LeetCode with browser cookies
make whoami    check LeetCode login status
make tmux      open or resume the 3-pane practice session
make venv      create/sync the Python environment
make ml        install optional ML dependencies
make clean     clear local cache directories
make repl      launch ipython
```

## What Lives Where

- Repo code and workflow scripts live here:
  `~/Codes/dsa-ml-practice`
- Generated LeetCode solutions live under:
  `~/Codes/dsa-ml-practice/leetcode`
- Generated solutions are git-ignored.
- tmux and Neovim configuration live in:
  `~/dotfiles`

## Requirements

- tmux
- Neovim
- Node/npm for the LeetCode CLI fork
- `inotifywait`
- `uv` for Python environment management

The LeetCode CLI used by this workflow is expected at
`~/Codes/leetcode-cli`.
