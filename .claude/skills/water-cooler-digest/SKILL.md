---
name: water-cooler-digest
description: >-
  Generate the day's "Water-Cooler Digest" — 6–8 quick-hit small-talk / ice-breaker
  items across the biggest sports, esports, and culture events happening right now,
  each with a specific, VERIFIED must-watch highlight clip. Writes a per-day JSON
  file and updates the manifest for the GitHub Pages site. Use for the daily routine
  or any time someone asks to refresh/build the digest.
---

# Water-Cooler Digest

You produce a daily small-talk briefing for Pavlo so he can chat easily with dads,
grown-ups, and coworkers. Pavlo is in the United States (Pacific time), so links must
work for a US viewer. The output is data for a static GitHub Pages site (in `docs/`).
Do NOT generate HTML — only JSON. The site renders it.

## Step 1 — Figure out "today"
Determine today's date in **America/Los_Angeles** (Pacific). Format it as
`YYYY-MM-DD` (e.g. `2026-06-19`). Call this `TODAY`. Build a friendly `label` like
`Friday, June 19, 2026`.

## Step 2 — Research what's actually big right now (use WebSearch — always search, never rely on memory)
Surface the biggest, most talked-about events happening **today and in the last
24–48 hours** — whatever they are. Do NOT force a fixed set of sports.

Pavlo's PRIORITY interests (give these the edge when they're in season and notable):
Soccer (Premier League, Champions League, World Cup, USMNT); US leagues (NBA, NFL,
NHL, MLB); Esports (Counter-Strike/CS2 majors especially, plus League/Valorant/Dota
majors).

ALWAYS scan the broader calendar too and pull in whatever is major right now,
especially when a priority sport is off-season: tennis Grand Slams; F1 and motorsport;
golf majors and the Ryder Cup; UFC/boxing; the Olympics & Paralympics; Tour de France
and cycling; cricket; college sports; WNBA, MLS, rugby. Also cover Culture & events:
music festivals, Burning Man, award shows, blockbuster movie/streaming releases, box
office. Run a "biggest sports events this week" type search to catch anything you'd miss.

**Be thorough — don't miss the marquee moments.** When a big event is on (e.g. a World
Cup), capture the specific standout performances everyone is talking about: hat-tricks,
walk-off home runs, buzzer-beaters, knockouts, last-lap passes, a star's big game (a
Messi/Mbappé hat-trick, a 50-point game, etc.). These individual moments are the best
ice-breakers. It's fine to include two items from the same sport on a huge day.

Verify any specific result (score, who scored, who won) with a second search before
stating it — search summaries sometimes garble names and scores.

## Step 3 — Pick 6–8 quick hits
Rank by how widely talked-about they are; spread across different events (don't do six
soccer items, but two is fine on a marquee day). Drop any priority category that's
off-season or has nothing notable and backfill with the next-biggest real story, so
the digest is always full, current, and comprehensive.

## Step 4 — Find the specific clip for each item — from an OFFICIAL, US-available source
For each item, find the ONE specific must-watch video — the exact moment everyone is
talking about. Two hard rules:

1. **Prefer official rights-holder / league channels.** These are stable and available
   in the US. Examples: FIFA, FOX Soccer, Telemundo Deportes, the Premier League, UEFA,
   NBA (and "NBA on ESPN"), NHL, MLB, NFL, Formula 1, ESPN, Sky Sports, TNT/Bleacher
   Report, ESL/BLAST and official team channels for esports. **Avoid unofficial
   reuploads** — random fan channels, and especially foreign broadcaster/telecom
   reuploads (e.g. channels like "Robi Axiata", regional TV rips). They are frequently
   **geo-blocked** (they play abroad but show "Video unavailable" in the US) or get
   taken down.
2. **Only use a video URL that literally appears in your search results — never invent
   or guess a YouTube video ID.**

## Step 5 — VERIFY every video actually plays in the US (required — do not skip)
For each candidate `https://www.youtube.com/watch?v=<ID>` link, verify it with your
shell before attaching it:

1. **Exists / not removed (oEmbed):**
   `curl -s -o /dev/null -w "%{http_code}" "https://www.youtube.com/oembed?url=https://www.youtube.com/watch?v=<ID>&format=json"`
   → must return `200`. `401` means embedding is disabled but the video is still
   watchable directly, which is acceptable. `404` means it's gone — drop it.
2. **Not geo-blocked for the US:** fetch the watch page and check the country list —
   `curl -s "https://www.youtube.com/watch?v=<ID>"` then look for `"availableCountries"`.
   If that list is present and does **not** include `"US"`, the video is region-locked
   away from the US → drop it. If the list is absent (no restriction) or includes `US`,
   it's fine. Also drop anything whose page shows `"status":"ERROR"` /
   `"UNPLAYABLE"` / "Video unavailable".
3. If a clip fails verification, find another (prefer the official channel). If you
   genuinely can't verify a working US clip, **do not attach a video** — use an
   official recap article or the league/tournament's "watch" page instead. Never ship
   an unverified video link.

Twitch channel links (e.g. `twitch.tv/esl_csgo`) for live esports don't need this check.

## Step 6 — Write the day file: `docs/days/TODAY.json`
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
  culture, f1, golf, tennis`. Any other value still renders (neutral color).
- `tag` is the visible label, format `Topic · Subtopic` (e.g. `Tennis · Wimbledon`).
- `emoji` one emoji that fits.
- `watch` is the small label above the links: `▶ The clip everyone saw` for a real
  highlight, `▶ Worth a look` / `▶ Watch it live` otherwise.
- `links` `kind`: `yt` (verified YouTube video, red), `tw` (Twitch, purple), `web`
  (source, green). Put the verified clip first, then 1 authoritative source. Every `yt`
  link MUST have passed Step 5.

## Step 7 — Update `docs/manifest.json`
Read it, then prepend `TODAY` to the `days` array if not already present, keeping the
array sorted newest-first (so the site opens on today by default). Set `updated` to the
current ISO timestamp. Keep the `repo` field.

Never delete old day files — the archive is the point.

## Step 8 — Commit & push
Stage `docs/days/TODAY.json` and `docs/manifest.json`, commit with message
`digest: TODAY`, and push to the default branch. GitHub Pages redeploys automatically.

## Tone
Warm, casual, a little fun — these are ice-breakers, not a news report. Avoid politics
and anything not suited to friendly small talk.
