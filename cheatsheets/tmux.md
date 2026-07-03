# tmux Cheat Sheet

> Tailored to your current `~/.tmux.conf` (Phoenix, WSL2). Prefix key is **`Ctrl+b`** — every command in this sheet starts with that unless noted.

## How to Read the Keystrokes

Three patterns appear throughout this sheet:

- **`Ctrl+b`** — Hold the **Ctrl** key, press **b**, release both. This is the "prefix" — every tmux command starts here.
- **`prefix X`** — Shorthand for: do the prefix (Ctrl+b), let go of both keys, *then* press the next key. For a capital like `prefix T` you press Ctrl+b, release both, then press **Shift+T**.
- **`prefix :`** then a command — Opens tmux's command line at the very bottom of the screen. After pressing `prefix :` you type the command literally and press **Enter**. (The colon is **Shift+;** on US keyboards.)

There's no chord — `Ctrl+b` always finishes before the next key. If nothing happens when you press the second key, the prefix may have timed out; press Ctrl+b again.

---

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

## Pane Titles — Step by Step

Your config shows a label bar at the top of each pane:

```
┌─ 0: Forge ────┬─ 1: Raven ───────┐
```

The trick: Claude Code and your shell constantly broadcast a "title" via hidden escape codes, which clobbers normal pane titles. Your config defends against this by reading a private tmux variable (`@custom_title`) that those programs can't see. **Visual cue**: the focused pane's border *and* title turn green/bold; inactive panes are dim gray.

### Set a locked title — keybinding route

1. Make sure the pane you want to label is the focused one (click it, or use `Ctrl+b` then an arrow key).
2. Press **`Ctrl+b`**, release both keys.
3. Press **`Shift+T`** (capital T).
4. Look at the very bottom of the tmux screen. A prompt appears:
   ```
   Pane title:
   ```
5. Type the label you want, e.g. `Dev Server`.
6. Press **Enter**.

The label appears in the top border of that pane. It will survive whatever the program inside it does.

### Set a locked title — manual route (no keybinding)

Use this if the keybinding misfires, or to script it.

1. Focus the pane you want to label.
2. Press **`Ctrl+b`**, release, then press **`Shift+;`** (the colon `:`).
3. A `:` prompt appears at the bottom.
4. Type literally (quotes included):
   ```
   set -p @custom_title "Dev Server"
   ```
5. Press **Enter**.

(The `-p` flag scopes the variable to the current pane only.)

### Clear a locked title

This hands the title back to whatever program is running (so e.g. `claude` will broadcast its own title again).

- **Keybinding route:** Press **`Ctrl+b`**, release, then press lowercase **`t`**.
- **Manual route:** Press **`Ctrl+b`** then **`:`**, type `set -p @custom_title ""` (two quotes, nothing between), press **Enter**.

### Rename an existing locked title

Just run the set keybinding again — `Ctrl+b` then `Shift+T` always overwrites. The prompt does *not* pre-fill with the old value; type the new title from scratch.

### Verify a title is set

Press **`Ctrl+b`** then **`:`**, type `show -pv @custom_title`, press **Enter**. The current value (or blank if cleared) prints at the bottom for a moment.

---

## Pane Colors — Step by Step

Two color things you can control:

1. **Pane background** — the color *inside* a pane (per-pane).
2. **Pane border color** — the line drawn around panes (global; your config already paints the focused pane's border green).

### Change the background color of the focused pane

1. Focus the pane you want to color (click it).
2. Press **`Ctrl+b`**, release, then press **`:`** (Shift+; on US keyboards). A `:` prompt appears at the bottom.
3. Type literally (quotes included):
   ```
   select-pane -P 'bg=colour235'
   ```
4. Press **Enter**. The background changes immediately.

Replace `colour235` with any 0–255 from the 256-color palette. Useful values:

| Color           | Code        | Good for                          |
| --------------- | ----------- | --------------------------------- |
| Dark gray       | `colour235` | Subtle dimming, easy on the eyes  |
| Almost black    | `colour232` | Maximum contrast against text     |
| Dark red        | `colour52`  | "Danger" / production pane        |
| Dark green      | `colour22`  | "Safe" / dev pane                 |
| Dark blue       | `colour17`  | Editor pane                       |
| Dark amber      | `colour94`  | Log-tailing pane                  |
| Plum            | `colour54`  | Distinctive secondary             |

### Reset the focused pane back to default

1. Press **`Ctrl+b`** then **`:`**.
2. Type: `select-pane -P 'bg=default'`
3. Press **Enter**.

### Color a specific pane without focusing it

Every pane has an index — the number in its title bar (`0:`, `1:`, …).

1. Press **`Ctrl+b`** then **`:`**.
2. Type (substituting the pane number you want):
   ```
   select-pane -t 1 -P 'bg=colour22'
   ```
3. Press **Enter**.

### Change the focused-pane border color (affects every pane, this session)

Your config draws focused borders in green. To switch to cyan for this tmux session:

1. Press **`Ctrl+b`** then **`:`**.
2. Type: `set -g pane-active-border-style fg=cyan`
3. Press **Enter**.

This lasts until tmux exits. To make it permanent, edit `~/.tmux.conf` and change the `pane-active-border-style` line, then reload with `Ctrl+b` then `r`.

### Pick a color you like

There's no built-in picker. Run this in a shell to print the full 256-color palette as labeled swatches, then plug the number you like into `colour<number>`:

```bash
for i in {0..255}; do printf "\e[48;5;${i}m %3d \e[0m" "$i"; (( (i+1) % 16 == 0 )) && echo; done
```

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
1. Press **`Ctrl+b`**, release, then press **`%`** (Shift+5) — splits vertically; cursor is now in the new right-hand pane.
2. Press **`Ctrl+b`**, release, then press **`Shift+T`**.
3. At the `Pane title:` prompt at the bottom of the screen, type `Dev Server`.
4. Press **Enter**. The label appears in the new pane's top border and will survive whatever the server spams to stdout.

**"My titles got hijacked by Claude Code"**
You didn't set a locked title. Re-set it: press **`Ctrl+b`**, release, then press **`Shift+T`**, type the label, press **Enter**. The `@custom_title` variable always wins over what Claude Code broadcasts.

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
