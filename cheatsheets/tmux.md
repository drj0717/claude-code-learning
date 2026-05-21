# tmux Cheat Sheet

> Tailored to your current `~/.tmux.conf` (Phoenix, WSL2). Prefix key is **`Ctrl+b`** — every command in this sheet starts with that unless noted.

## Mental Model

tmux has three layers, nested like Russian dolls:

| Layer       | What it is                                              | Lives where      |
| ----------- | ------------------------------------------------------- | ---------------- |
| **Session** | A long-running workspace (survives terminal closing)    | The tmux server  |
| **Window**  | Like a browser tab inside a session                     | Inside a session |
| **Pane**    | A split region inside a window — where a shell runs     | Inside a window  |

You attach and detach from **sessions**. You switch between **windows**. You split and navigate **panes**.

---

## Sessions (the outermost layer)

Run these from a regular shell (no prefix needed):

| Command                       | What it does                              |
| ----------------------------- | ----------------------------------------- |
| `tmux`                        | Start a new unnamed session               |
| `tmux new -s work`            | Start a new session named `work`          |
| `tmux ls`                     | List all running sessions                 |
| `tmux a` or `tmux attach`     | Attach to the most recent session         |
| `tmux a -t work`              | Attach to session named `work`            |
| `tmux kill-session -t work`   | Kill a session by name                    |
| `tmux kill-server`            | Nuke everything (all sessions, all panes) |

Inside tmux:

| Keys           | What it does                                |
| -------------- | ------------------------------------------- |
| `prefix d`     | **Detach** from session (leaves it running) |
| `prefix s`     | Pick a session from a list                  |
| `prefix $`     | Rename current session                      |

---

## Windows (tabs)

| Keys             | What it does                       |
| ---------------- | ---------------------------------- |
| `prefix c`       | **Create** a new window            |
| `prefix ,`       | Rename the current window          |
| `prefix n`       | **Next** window                    |
| `prefix p`       | **Previous** window                |
| `prefix 0`–`9`   | Jump to window by number           |
| `prefix w`       | Pick a window from a list          |
| `prefix &`       | Kill the current window (asks y/n) |

---

## Panes (splits)

| Keys                   | What it does                                  |
| ---------------------- | --------------------------------------------- |
| `prefix %`             | Split **vertically** (new pane on the right)  |
| `prefix "`             | Split **horizontally** (new pane below)       |
| `prefix ←↑↓→`          | Move focus to the pane in that direction      |
| `prefix o`             | Cycle through panes                           |
| `prefix z`             | **Zoom** the current pane (toggle fullscreen) |
| `prefix x`             | Kill the current pane (asks y/n)              |
| `prefix {` / `prefix }`| Swap pane with the previous / next one        |
| `prefix space`         | Cycle through preset layouts                  |
| `prefix Ctrl+←↑↓→`     | Resize pane by 1 cell (hold Ctrl, repeat)     |
| `prefix Alt+←↑↓→`      | Resize pane by 5 cells                        |

You also have **mouse on** — click a pane to focus, drag a border to resize, scroll wheel works in scrollback.

---

## Pane Titles (the "bulletproof" labels)

Your config shows a title bar at the top of each pane:

```
┌─ 0: Forge ────┬─ 1: Raven ───────┐
```

The trick: Claude Code and your shell constantly broadcast a "title" via hidden escape codes, which clobbers normal pane titles. Your config defends against this by reading a private tmux variable (`@custom_title`) that those programs can't see.

| Keys                     | What it does                                                 |
| ------------------------ | ------------------------------------------------------------ |
| `prefix T` *(capital)*   | Prompt for a **locked** title for the current pane           |
| `prefix t` *(lowercase)* | Clear the lock — hand the title back to the running program  |

**Manual form** (no keybinding):
- Lock a title: `prefix :` then `set -p @custom_title "Project Raven"`
- Clear it:     `prefix :` then `set -p @custom_title ""`

The `-p` flag scopes the variable to the current **p**ane.

**Visual cue**: the focused pane's border *and* title turn green/bold. Inactive panes are dim gray.

---

## Copy Mode & Clipboard

Copy mode is how you scroll back and copy text inside tmux.

| Keys                   | What it does                                              |
| ---------------------- | --------------------------------------------------------- |
| `prefix [`             | Enter copy mode                                           |
| Arrow keys / `Page Up` | Move around / scroll                                      |
| `space`                | Start a selection                                         |
| `enter`                | Copy the selection (and exit copy mode)                   |
| `q`                    | Exit copy mode without copying                            |
| `prefix ]`             | Paste the most recent copy                                |

**Mouse-based copy (your config):**
- Just drag with the mouse → enters copy mode, selects, and on release pipes the text to **`clip.exe`** (Windows clipboard). You can paste in any Windows app with `Ctrl+V`.
- **Shift-drag** → bypasses tmux entirely and uses native terminal selection (also copies to the system clipboard, but uses the terminal's own selection model).

---

## Config & Reload

Your config file: `~/.tmux.conf`

| Keys                  | What it does                              |
| --------------------- | ----------------------------------------- |
| `prefix r`            | Reload `~/.tmux.conf` (shows confirmation)|
| `prefix :`            | Open the tmux command prompt              |
| `prefix ?`            | Show every keybinding (q to exit)         |

To apply changes after editing the config without reloading: `prefix :` then `source-file ~/.tmux.conf`.

---

## What's in Your Config Right Now

A quick map so you know what's "vanilla tmux" vs. "yours":

| Setting                                  | Why it's there                                            |
| ---------------------------------------- | --------------------------------------------------------- |
| `default-terminal "tmux-256color"` + Tc  | True color so Claude Code syntax highlighting renders     |
| `history-limit 50000`                    | Big scrollback for reviewing long Claude output           |
| `mouse on`                               | Click, drag, scroll work                                  |
| `escape-time 10`                         | Faster `Esc` response for vim / TUIs                      |
| `pane-border-status top`                 | Shows the title bar at the top of each pane               |
| `pane-border-format ...`                 | Locked-title aware formatter (bulletproof titles)         |
| `pane-active-border-style fg=green`      | Focused pane border is green                              |
| `bind r`                                 | `prefix r` reloads the config                             |
| `bind T`                                 | `prefix T` sets a locked pane title                       |
| `bind t`                                 | `prefix t` clears the locked title                        |
| `bind-key -T copy-mode MouseDragEnd1Pane`| Mouse drag in copy mode pipes to `clip.exe`               |

---

## Recipes

**"I want to set up a project workspace and detach when done"**
```bash
tmux new -s raven         # new session named 'raven'
# ... split panes with prefix % / prefix "
# ... label them with prefix T
prefix d                  # detach (everything keeps running)
# later:
tmux a -t raven           # attach again, exactly where you left off
```

**"I split a pane to run a server and I want it labeled"**
1. Split: `prefix %`
2. Focus the new pane (`prefix →`)
3. Label it: `prefix T` → type `Dev Server` → Enter
4. The label survives even when the server starts spamming output.

**"My titles got hijacked by Claude Code"**
You didn't set a locked title. Re-set with `prefix T`. The `@custom_title` variable always wins over what Claude Code broadcasts.

**"I want a project per window, with multiple panes each"**
```
Session: work
├── Window 0: forge     (3 panes: editor / dev server / git)
├── Window 1: raven     (2 panes: editor / logs)
└── Window 2: scratch
```
Jump between projects with `prefix 0` / `prefix 1` / `prefix 2`.

---

## Things to Know Later (Not Now)

When you're comfortable with the above, look into:

- **`tmux-resurrect` / `tmux-continuum`** — auto-save and restore sessions across reboots
- **Custom status bar** (`status-left`, `status-right`) — show CPU, time, git branch
- **Pane synchronization** (`setw synchronize-panes`) — type once, broadcast to all panes
- **Named layouts** — save and recall pane arrangements
- **vi mode** (`set -g mode-keys vi`) — vim-style navigation in copy mode

---

*Last updated against `~/.tmux.conf` on 2026-05-21.*
