# LeetCode Workflow Compendium

This is the detailed guide for the `dsa-ml-practice` workflow. It is
written as a practical playbook: what to press, what happens, where files
go, and what to do when you come back later.

The short version is:

```bash
cd ~/Codes/dsa-ml-practice
make tmux
```

Then use the left pane to pick problems, the top-right pane to edit, and
the bottom-right pane to test and submit.

## Mental Model

This repo gives you one named tmux session:

```text
dsa-ml-practice
```

Inside that session, each normal practice window has three panes:

```text
+-------------+---------------------------+
| LEFT        | TOP-RIGHT                 |
| leetcode    | nvim                      |
| TUI         | current solution file     |
|             +---------------------------+
|             | BOTTOM-RIGHT              |
|             | shell for test/submit     |
+-------------+---------------------------+
```

The left pane is for choosing problems. The top-right pane is for
editing code. The bottom-right pane is for commands and output.

The workflow glue is:

- `lc-watch` watches for newly picked solution files.
- When the TUI creates a fresh primary `.py` file, `lc-watch` opens it
  in Neovim.
- `lc-watch` also writes the current file path to
  `leetcode/.current/path`.
- `C-a T` and `C-a S` read that path and run test/submit against the
  exact current file.

That last point matters. The workflow targets the current file path, not
just the problem number. That prevents old numbered attempts from being
tested by accident.

## First-Time Setup

Run this once:

```bash
cd ~/Codes/dsa-ml-practice
make setup
make login
```

`make setup` does this:

- builds and links the LeetCode CLI fork from `~/Codes/leetcode-cli`
- creates or reuses the `dsa-ml-practice` LeetCode workspace
- points that workspace at `~/Codes/dsa-ml-practice/leetcode`
- sets Python 3 and Neovim as the default language/editor

`make login` runs:

```bash
leetcode login
```

It asks for browser cookie values from LeetCode. After logging in, you
can check auth with:

```bash
make whoami
```

## Starting The Workspace

Use:

```bash
make tmux
```

This is the recommended entry point. It is better than plain
`tmux attach` because it runs the repo repair script first.

`make tmux` does this:

1. ensures the `dsa-ml-practice` tmux session exists
2. creates the standard 3-pane layout if needed
3. repairs a stale or half-restored first window when it can
4. starts the file watcher in the bottom-right pane if missing
5. attaches from a normal terminal
6. switches clients if you are already inside another tmux session

### If You Do Not Use `make tmux`

You can attach manually if the session already exists:

```bash
tmux attach -t dsa-ml-practice
```

If you are already inside another tmux session, use:

```bash
tmux switch-client -t dsa-ml-practice
```

Manual attach skips the repair step. If the panes are weird, use:

```bash
cd ~/Codes/dsa-ml-practice
make tmux
```

That is the plug-and-play path.

## The Main Key Bindings

The tmux prefix is `C-a`, meaning press `Ctrl-a`, release, then press the
next key.

| binding | action |
| --- | --- |
| `C-a T` | test the current solution |
| `C-a S` | submit the current solution |
| `C-a c` | create a new practice window |
| `C-a n` | next window |
| `C-a p` | previous window |
| `C-a w` | list windows |
| `C-a d` | detach |
| `C-a C-s` | manually save tmux session state |
| `C-a r` | reload tmux config |
| `C-a [` | enter tmux copy-mode |
| `q` or `Esc` | leave tmux copy-mode |

`C-a T` and `C-a S` only act inside the `dsa-ml-practice` session. If
you press them in another tmux session, they exit without sending
LeetCode commands into that session.

## Normal Daily Flow

1. Start:

   ```bash
   cd ~/Codes/dsa-ml-practice
   make tmux
   ```

2. In the left pane, browse or search for a problem.

3. Press `p` on the problem.

4. Wait for the top-right Neovim pane to open the solution file.

5. Write code and save it in Neovim.

6. Press `C-a T`.

7. Read test output in the bottom-right pane.

8. If tests pass, press `C-a S`.

9. Detach with `C-a d` when done.

## Next Day: Continue The Same Solution

If you come back the next day and want to continue the same answer:

1. Run `make tmux`.
2. If Neovim still has the file open, keep editing.
3. Save in Neovim.
4. Press `C-a T` to test.
5. Press `C-a S` to submit.

The current active file is the unnumbered file:

```text
1.two-sum.py
```

If that is the file open in Neovim, you are editing the active attempt.

## Next Day: Try A Different Solution To The Same Problem

This is the workflow you asked about.

Imagine yesterday you solved Two Sum and it passed. Today you reopen the
session and Neovim still shows the correct old answer. You now want a
fresh attempt.

Recommended flow:

1. Go to the left pane.
2. Navigate to the same problem in the LeetCode TUI.
3. Press `p` again.
4. The old current file is renamed to a numbered archive.
5. A fresh unnumbered file is created.
6. Neovim opens the fresh unnumbered file automatically.

Example:

```text
before:
1.two-sum.py          # yesterday's working solution

after pressing p again:
1.two-sum.1.py        # archived old solution
1.two-sum.py          # fresh current attempt
```

So yes: go to the problem pane and press `p`. That is the recommended
way to start another attempt for the same problem.

Do not manually create `1.two-sum.2.py` as your active file. Numbered
files are archives. The workflow treats the unnumbered file as current.

## How Numbering Works

The rule is simple:

```text
unnumbered file = current attempt
numbered file   = archived older attempt
```

Example:

```text
1.two-sum.py
1.two-sum.1.py
1.two-sum.2.py
1.two-sum.3.py
```

Meaning:

- `1.two-sum.py` is the active attempt.
- `1.two-sum.1.py` is the first archived attempt.
- `1.two-sum.2.py` is the second archived attempt.
- `1.two-sum.3.py` is the third archived attempt.

The numbering is not backwards. The higher number is newer among the
archives, but the unnumbered file is still the current one.

## Testing And Submitting

Use:

```text
C-a T
```

to test.

Use:

```text
C-a S
```

to submit.

Both commands target the path stored in:

```text
leetcode/.current/path
```

That file is updated when you press `p` in the TUI and a fresh primary
solution file is created.

The output appears in the bottom-right pane.

If the bottom-right pane is in tmux copy-mode, the scripts cancel
copy-mode before sending commands. This prevents the old "jump forward"
message caused by tmux interpreting the `t` in `leetcode test`.

## Multiple Problems At The Same Time

Use one tmux window per problem.

Create a new practice window:

```text
C-a c
```

That new window gets the same three panes:

- left: LeetCode TUI
- top-right: Neovim
- bottom-right: shell and watcher

Pick a different problem in the new window by pressing `p` in that
window's left pane.

Switch windows with:

```text
C-a n      next window
C-a p      previous window
C-a w      window list
C-a 1      window 1
C-a 2      window 2
```

### Are New Windows Saved?

Yes, tmux-continuum and tmux-resurrect are enabled in the dotfiles.
They save tmux session state periodically.

For best results before shutting down:

```text
C-a C-s
C-a d
```

Then shut down.

After booting again:

```bash
cd ~/Codes/dsa-ml-practice
make tmux
```

tmux restore is not magic. It usually restores windows, panes, commands,
and pane contents, but terminal apps can still come back imperfectly
after a hard shutdown. `make tmux` runs the repair step and gives you a
healthy standard window if the saved state is stale.

## Other tmux Sessions

This workflow uses a named session:

```text
dsa-ml-practice
```

Your other tmux sessions can still exist. This repo does not take over
the whole tmux server.

The session-specific behavior is:

- `make tmux` creates or repairs only the `dsa-ml-practice` session.
- new-window auto-layout is installed as a session hook for
  `dsa-ml-practice`
- `C-a T` and `C-a S` refuse to run outside `dsa-ml-practice`

The normal tmux bindings, such as `C-a c`, still exist globally because
they are part of your tmux config. The LeetCode-specific behavior is
guarded by the helper scripts.

If you are inside another tmux session and run:

```bash
cd ~/Codes/dsa-ml-practice
make tmux
```

tmux switches your current client to `dsa-ml-practice`. It does not nest
tmux inside tmux.

To go back to another session, use:

```text
C-a s
```

or:

```bash
tmux switch-client -t <session-name>
```

## File Locations

Tracked repo files:

```text
~/Codes/dsa-ml-practice
```

Generated LeetCode solution files:

```text
~/Codes/dsa-ml-practice/leetcode
```

Current solution sentinel:

```text
~/Codes/dsa-ml-practice/leetcode/.current/path
```

LeetCode workspace config and snapshots:

```text
~/.leetcode/workspaces/dsa-ml-practice
```

tmux and Neovim config source:

```text
~/dotfiles
```

Generated solution files are git-ignored. They are your practice work,
not repo source code.

## Snapshots

The CLI also has explicit snapshots:

```bash
leetcode snapshot save 1 brute-force
leetcode snapshot save 1 hashmap
leetcode snapshot list 1
leetcode snapshot restore 1 brute-force
leetcode snapshot diff 1 1 2
```

Use snapshots when you want named versions. Use the normal `p` re-pick
workflow when you just want a fresh attempt.

## Recommended Habits

- Start with `make tmux`.
- Use one tmux window per active problem.
- Press `p` to create or refresh the active attempt.
- Treat the unnumbered file as current.
- Treat numbered files as archived attempts.
- Save in Neovim before testing.
- Use `C-a T` for test and `C-a S` for submit.
- Press `C-a C-s` before shutting down if you care about restoring the
  tmux layout.
- Use `make tmux` after reboot instead of direct `tmux attach`.

## Troubleshooting

### `C-a T` or `C-a S` does nothing

Check that you are inside the `dsa-ml-practice` tmux session:

```bash
tmux display-message -p '#S'
```

If not, run:

```bash
cd ~/Codes/dsa-ml-practice
make tmux
```

### Bottom-right pane feels stuck in visual mode

That is tmux copy-mode.

Manual fix:

```text
q
```

or:

```text
Esc
```

The test/submit scripts also cancel copy-mode automatically before
running commands.

### Neovim did not open the picked file

Run:

```bash
cd ~/Codes/dsa-ml-practice
make tmux
```

That restarts the watcher if it is missing.

Then pick the problem again with `p`.

### I attached manually and the panes look wrong

Use:

```bash
cd ~/Codes/dsa-ml-practice
make tmux
```

Manual `tmux attach` skips the repair step.

### I want a totally fresh practice window

Inside the session:

```text
C-a c
```

Then pick a problem in the new left pane.

### I want to inspect old attempts

List them:

```bash
find ~/Codes/dsa-ml-practice/leetcode -name '1.two-sum*' -print
```

Open an archived file manually in Neovim if you want to read it. Keep in
mind that test/submit uses the current unnumbered file, not archives.

## Command Reference

```text
make help      show commands
make setup     install CLI and configure workspace
make install   build/link the LeetCode CLI fork
make config    point CLI workspace at this repo
make login     log in to LeetCode
make whoami    check login status
make tmux      open or resume the practice session
make venv      create/sync Python env
make ml        install optional ML dependencies
make clean     clear caches
make repl      launch ipython
```

## Design Rules

This workflow is intentionally narrow:

- one named tmux session
- one standard layout
- one active solution path
- no manual pane setup
- no guessing which numbered file to test

When in doubt, return to the simple path:

```bash
cd ~/Codes/dsa-ml-practice
make tmux
```
