# Water-Cooler Digest — agent instructions

This repo powers a daily small-talk / ice-breaker digest published as a static
GitHub Pages site from `docs/`.

## When asked to "update the digest", "run the daily digest", or on the scheduled routine
Use the **water-cooler-digest** skill in `.claude/skills/water-cooler-digest/SKILL.md`
and follow it exactly. In short:
1. Determine today's date in **America/Los_Angeles** (Pacific).
2. Research the biggest sports / esports / culture stories happening now (WebSearch).
3. Find a specific must-watch highlight clip for each item (never invent YouTube IDs).
4. Write `docs/days/<YYYY-MM-DD>.json` (5–7 items, schema in the skill).
5. Prepend the date to `docs/manifest.json` (newest first) and update `updated`.
6. Commit (`digest: <date>`) and push to the default branch.

## Project layout
- `docs/index.html` — the site (single page; reads JSON, renders cards, day switcher).
- `docs/manifest.json` — list of available days, newest first. Drives the menu and default.
- `docs/days/<date>.json` — one file per day (the archive). Never delete these.
- `.claude/skills/water-cooler-digest/` — the skill that generates a day.
- `.github/workflows/daily-digest.yml` — OPTIONAL GitHub Actions fallback (off by default).

## Rules
- Only ever add/modify files under `docs/` when generating a digest. Don't touch the
  site template (`docs/index.html`) unless explicitly asked to change the design.
- Never delete past day files. The browsable archive is the whole point.
- Keep the manifest sorted newest-first so the site opens on the latest day.
