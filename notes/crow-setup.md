# Crow Machine Setup — Pending Actions
_Saved 2026-02-25 — continue this on crow in THIS (claude) project first_

---

## Context
Cross-machine sync (open/close-session skills) is working. Pausing youtube work
until sync is fully smooth. Complete these tasks first, then return to youtube.

## Action List

### 1. Set ~/.claude default pull strategy
```bash
cd ~/.claude && git config pull.rebase true
```
Prevents the "divergent branches" prompt on every `git pull`.

### 2. Check ~/.claude state before close-session
Phoenix made a direct commit to `~/.claude` (crow youtube MEMORY.md) that
wasn't pushed — close-session may encounter a diverged `~/.claude` and need
to pull --rebase before pushing. This is expected; let it handle it.

### 3. Install CLI tools on crow
```bash
sudo apt install ffmpeg gh
sudo curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp \
  -o /usr/local/bin/yt-dlp && sudo chmod +x /usr/local/bin/yt-dlp
```
Verify: `ffmpeg -version && yt-dlp --version && gh --version`

### 4. Install Python packages on crow
```bash
pip3 install youtube-transcript-api requests
```
Verify: `python3 -c "from youtube_transcript_api import YouTubeTranscriptApi; print('ok')"`

### 5. Create Windows output directory on crow (if missing)
```bash
mkdir -p /mnt/c/Users/drj07/Videos/yt-analyzer
```

### 6. Investigate always-modified plugin files in ~/.claude
`plugins/blocklist.json` and `plugins/known_marketplaces.json` always show as
modified, blocking git pulls. Check if they should be gitignored:
```bash
cd ~/.claude && git diff plugins/
```
If the diffs are trivial/auto-generated: add them to `~/.claude/.gitignore`.

### 7. Smoke test youtube-analyzer on crow
Run the skill on a short video to confirm the full toolchain works end-to-end.

---

## Note on crow /youtube newer work
Crow's `/home/crow/projects/youtube` has commits newer than what's on remote.
Do NOT open that project yet — finish sync cleanup here first.
When ready: open-session will detect "local ahead" and skip pull, then
close-session will push crow's newer commits up.
