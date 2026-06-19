---
name: water-cooler-digest
description: >-
  Generate the day's "Water-Cooler Digest" — 5–7 quick-hit small-talk / ice-breaker
  items across the biggest sports, esports, and culture events happening right now,
  each with a specific must-watch highlight clip. Writes a per-day JSON file and
  updates the manifest for the GitHub Pages site. Use for the daily routine or any
  time someone asks to refresh/build the digest.
---

# Water-Cooler Digest

You produce a daily small-talk briefing for Pavlo so he can chat easily with dads,
grown-ups, and coworkers. The output is data for a static GitHub Pages site (in
`docs/`). Do NOT generate HTML — only JSON. The site renders it.

## Step 1 — Figure out "today"
Determine today's date in **America/Los_Angeles** (Pacific). Format it as
`YYYY-MM-DD` (e.g. `2026-06-19`). Call this `TODAY`. Also build a friendly
`label` like `Friday, June 19, 2026`.

## Step 2 — Research what's actually big right now (use WebSearch — always search, never rely on memory)
Surface the biggest, most talked-about events happening **today and in the last
24–48 hours** — whatever they are. Do NOT force a fixed set of sports.

Pavlo's PRIORITY interests (give these the edge when they're in season and have
something notable): Soccer (Premier League, Champions League, World Cup, USMNT);
US leagues (NBA, NFL, NHL, MLB); Esports (Counter-Strike/CS2 majors especially,
plus League/Valorant/Dota majors).

But ALWAYS scan the broader calendar and pull in whatever is major right now,
especially when a priority sport is off-season or quiet. Seasonal menu to check
(not exhaustive): tennis Grand Slams (Australian Jan, Roland-Garros May–Jun,
Wimbledon Jun–Jul, US Open Aug–Sep); F1 and motorsport race weekends; golf majors
(Masters Apr, PGA May, US Open Jun, The Open Jul) and the Ryder Cup; UFC/boxing
marquee cards; Olympics & Paralympics; Tour de France and cycling; cricket (IPL,
T20/ODI World Cups); college sports (March Madness, CFP); WNBA, MLS, rugby, etc.
Run a "biggest sports events this week" type search to catch anything you'd miss.
Also cover Culture & events: music festivals, Burning Man, award shows, blockbuster
movie/streaming releases, box office, and other broadly-known happenings.

Verify any specific result with a second query before stating it — search summaries
sometimes garble team names and scores.

## Step 3 — Pick 5–7 quick hits
Rank by how widely talked-about they are; spread across different events (don't do
five soccer items). Drop any priority category that's off-season or has nothing
notable and backfill with the next-biggest real story, so the digest is always full
and current. Aim for variety: a couple priority-sport items + 1–2 "whatever's huge
right now" items + 1 culture item.

## Step 4 — Find the specific clip for each item (most important part)
For every item, find the ONE specific must-watch video — the exact moment everyone
is talking about (clinching play, viral goal, walk-off, knockout, final-lap pass) —
NOT a YouTube search page. Search `<event> highlights youtube` and take the actual
`https://www.youtube.com/watch?v=...` URL that appears in the results, preferring
official channels (NBA on ESPN, NHL, FIFA/FOX Soccer, F1, ESL/team channels).
**CRITICAL: only use a video URL that literally appears in your search results —
never invent or guess a YouTube video ID.** If you truly can't find a specific clip
(common for same-day festival sets or upcoming events), fall back to a specific
authoritative recap article or official video; only as a last resort use a
tag/search URL. Twitch channel links (e.g. `twitch.tv/esl_csgo`) are fine for live
esports.

## Step 5 — Write the day file: `docs/days/TODAY.json`
Match this schema exactly:

```json
{
  "date": "2026-06-19",
  "label": "Friday, June 19, 2026",
  "items": [
    {
      "cat": "soccer",
      "emoji": "⚽",
      "tag": "Soccer · World Cup",
      "headline": "Short, punchy headline",
      "body": "1–2 sentences of context.",
      "say": "A casual opener Pavlo can actually say out loud.",
      "watch": "▶ The clip everyone saw",
      "links": [
        { "kind": "yt",  "text": "Label", "url": "https://www.youtube.com/watch?v=..." },
        { "kind": "web", "text": "Source", "url": "https://..." }
      ]
    }
  ]
}
```

Field rules:
- `cat` drives the tag color. Use one of: `soccer, us, nba, nfl, nhl, mlb, esports,
  culture, f1, golf, tennis`. Any other value still renders (neutral color), so you
  may invent a category if needed — but prefer the list.
- `tag` is the visible label, format `Topic · Subtopic` (e.g. `Tennis · Wimbledon`).
- `emoji` one emoji that fits.
- `watch` is the small label above the links: `▶ The clip everyone saw` for a real
  highlight, or `▶ Worth a look` / `▶ Watch it live` when appropriate.
- `links` `kind`: `yt` (YouTube video, red), `tw` (Twitch, purple), `web` (source,
  green). Put the specific clip first, then 1 authoritative source.

## Step 6 — Update `docs/manifest.json`
Read it, then prepend `TODAY` to the `days` array if not already present, keeping the
array sorted newest-first (so the site opens on today by default). Set `updated` to
the current ISO timestamp. Keep the `repo` field. Example:

```json
{ "repo": "https://github.com/agent-pasha/water-cooler-digest",
  "updated": "2026-06-19T09:00:00-07:00",
  "days": ["2026-06-19", "2026-06-15"] }
```

Never delete old day files — the archive is the point.

## Step 7 — Commit & push
Stage `docs/days/TODAY.json` and `docs/manifest.json`, commit with message
`digest: TODAY`, and push to the default branch. GitHub Pages redeploys
automatically. If running inside a routine that handles git itself, just leave the
files written and let it commit.

## Tone
Warm, casual, a little fun — these are ice-breakers, not a news report. Avoid
politics and anything not suited to friendly small talk.
