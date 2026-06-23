# ☕ Water-Cooler Digest

A daily small-talk / ice-breaker briefing — 5–7 quick hits across the biggest
sports, esports, and culture events happening right now, each with the one
highlight clip everybody's actually watching, plus a casual "Ice breaker:" opener.

A **Claude Code routine** runs every morning at **9:00 AM Pacific**, researches the
day's events (web search), finds specific must-watch clips, writes a new day file,
and pushes it here. **GitHub Pages** serves the site, which opens on the latest day
and lets you browse every past day from a menu.

**Live site:** https://agent-pasha.github.io/water-cooler-digest/

## How it works
- `docs/` is the published site (GitHub Pages).
  - `index.html` — the whole site: loads the data, renders the cards, and provides the
    day switcher (Older / menu / Newer). The newest day opens by default.
  - `manifest.json` — list of available days, newest-first. Drives the menu + default.
  - `days/<YYYY-MM-DD>.json` — one file per day (the archive). Nothing is ever deleted.
- `.claude/skills/water-cooler-digest/` — the **skill** the routine runs each morning.
  It defines the research, the "specific clip" rule, the JSON schema, and the manifest
  update. Editing this changes what future digests look like.
- `CLAUDE.md` — repo-level instructions so any Claude Code session knows how to update
  the digest.

The routine only writes data (`docs/days/*.json` + `manifest.json`). It never
regenerates the HTML, which keeps daily runs reliable.

## One-time setup

### 1. Enable GitHub Pages
Repo **Settings → Pages → Build and deployment**: Source = *Deploy from a branch*,
Branch = `main`, Folder = `/docs`. Save. After a minute the site is live at the URL
above. (A `.nojekyll` file is included so JSON in folders is served as-is.)

### 2. Create the Claude Code routine
This is the cloud scheduler. At **https://claude.ai/code/routines**:
- New routine → connect this repo (`agent-pasha/water-cooler-digest`).
- Schedule: **daily, 9:00 AM, America/Los_Angeles (Pacific)**.
- Prompt:
  > Run the water-cooler-digest skill in this repo to generate today's digest:
  > determine today's date in America/Los_Angeles, research the biggest current
  > sports/esports/culture stories with specific highlight clips, write
  > `docs/days/<date>.json` (5–7 items per the skill schema), prepend the date to
  > `docs/manifest.json` (newest first), then commit (`digest: <date>`) and push.
- Give it permission to use web search and to commit/push to the repo.
- Run it once manually to confirm it produces a new day and the site updates.

### Alternative: GitHub Actions (instead of the routine)
A ready-made workflow is at `.github/workflows/daily-digest.yml`. It's manual-only by
default. To use it as your scheduler: add a repo secret `ANTHROPIC_API_KEY`
(Settings → Secrets and variables → Actions), uncomment the `schedule:` block in the
workflow, and turn the Claude Code routine off so they don't both run. Note: GitHub
cron is UTC and ignores daylight saving, so `0 16 * * *` is 9 AM Pacific in summer and
8 AM in winter.

## Editing
- Change what's covered or the tone → edit `.claude/skills/water-cooler-digest/SKILL.md`.
- Change the look → edit `docs/index.html` (CSS + render logic are inline).
- Add a category color → add a `.t-<cat>` rule in `index.html` and use that `cat`.

## Data schema
Each `docs/days/<date>.json`:
```json
{
  "date": "2026-06-19",
  "label": "Friday, June 19, 2026",
  "items": [
    {
      "cat": "soccer",
      "emoji": "⚽",
      "tag": "Soccer · World Cup",
      "headline": "…",
      "body": "…",
      "say": "…",
      "watch": "▶ The clip everyone saw",
      "links": [
        { "kind": "yt",  "text": "…", "url": "https://www.youtube.com/watch?v=…" },
        { "kind": "web", "text": "…", "url": "https://…" }
      ]
    }
  ]
}
```
`kind`: `yt` (YouTube, red), `tw` (Twitch, purple), `web` (source, green).
`cat`: `soccer, us, nba, nfl, nhl, mlb, esports, culture, f1, golf, tennis` (others
render in a neutral color).
