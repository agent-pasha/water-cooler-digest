---
name: water-cooler-digest
description: >-
  Generate the day's "Water-Cooler Digest" — ~8–10 quick-hit small-talk / ice-breaker
  items spanning the biggest sports, esports, and culture happening right now (with
  3–5 slots reserved for culture), each with a specific, VERIFIED must-watch clip,
  preferring YouTube. Writes a per-day JSON file and updates the manifest for the
  GitHub Pages site. Use for the daily routine or any time someone asks to
  refresh/build the digest.
---

# Water-Cooler Digest

You produce a daily small-talk briefing for Pavlo so he can chat easily with dads,
grown-ups, and coworkers. Pavlo is in the United States (Pacific time), so links must
work for a US viewer. **Pavlo has YouTube Premium (ad-free) and dislikes broadcaster
sites like FOX/ESPN because of their ads — so prefer a working YouTube link wherever
one exists.** The output is data for a static GitHub Pages site (in `docs/`). Do NOT
generate HTML — only JSON. The site renders it.

## Step 1 — Figure out "today"
Determine today's date in **America/Los_Angeles** (Pacific). Format it as
`YYYY-MM-DD` (e.g. `2026-06-19`). Build a friendly `label` like `Friday, June 19, 2026`.

## Step 2 — Research what's actually big right now (use WebSearch — always search)
Surface the biggest, most talked-about things happening **today and in the last
24–48 hours**.

SPORTS & ESPORTS (aim for ~5 items): Pavlo's priorities are soccer (Premier League,
Champions League, World Cup, USMNT), US leagues (NBA, NFL, NHL, MLB), and esports
(Counter-Strike/CS2 majors especially). Also scan the broader calendar and pull in
whatever is major right now — tennis Grand Slams, F1/motorsport, golf majors, UFC/
boxing, Olympics, cycling, cricket, college sports. **Capture the marquee individual
moments everyone is talking about** (a Messi/Mbappé hat-trick, a walk-off, a buzzer-
beater, a knockout). It's fine to have two items from the same sport on a huge day.

CULTURE (aim for 3–5 items — this is important; do NOT collapse culture into one item):
cover the full cultural conversation, e.g.
- **Film** — major theatrical openings (a new Pixar/Disney/Marvel/DC film, etc.) and the
  weekend box-office winner. Never miss a big opening (e.g. a Toy Story release).
- **Music** — notable new albums / "New Music Friday", big tours, festivals (Bonnaroo,
  Glastonbury, Coachella).
- **TV / streaming** — buzzy premieres, finales, and returning hit shows.
- Plus awards shows or other broadly-known happenings when relevant.

Verify any specific result (score, who scored, release date, who won) with a second
search before stating it.

## Step 3 — Pick ~8–10 quick hits
Roughly 5 sports/esports + 3–5 culture. Rank by how widely talked-about they are and
spread across topics so the digest is full, current, and comprehensive.

## Step 4 — Find a specific clip per item, preferring YouTube
For each item, find the ONE specific must-watch video — the exact moment/trailer
everyone is talking about. **Only use a video URL that literally appears in your search
results — never invent a YouTube ID.** Pick the source in this order:

1. **A verified, US-available YouTube video** (preferred — Pavlo has Premium). Best when
   it's from an official channel: the studio (e.g. Pixar, Marvel), the artist/label
   (official "VEVO"/artist channels), the league/broadcaster's own YouTube (NBA, NHL,
   MLB, F1, FOX Soccer), or the event organizer (ESL/BLAST). Movie trailers and music
   videos are almost always on official, globally-available YouTube channels — use them.
2. **Only if no working US YouTube exists**, use the official broadcaster's own page
   (e.g. foxsports.com, espn.com). Note for the rights-heavy World Cup, US YouTube
   highlights are often unavailable, so FOX Sports may be the only working option — that
   is an acceptable fallback.
3. If neither works, use an authoritative recap article. Never ship an unverified or
   broken video link.

Twitch channel links (e.g. `twitch.tv/esl_csgo`) for live esports are fine as-is.

## Step 5 — VERIFY every YouTube link actually plays in the US (required — do not skip)
For each `https://www.youtube.com/watch?v=<ID>` candidate, before attaching it, run:

1. **Exists / not removed (oEmbed):**
   `curl -s "https://www.youtube.com/oembed?url=https://www.youtube.com/watch?v=<ID>&format=json"`
   → must return JSON containing a `"title"` (HTTP 200). An empty body or a 404 means it's
   gone/private → drop it. The JSON's `author_name` also tells you the channel — prefer
   official channels and avoid random foreign reuploads (a common geo-block cause).
2. **Not geo-blocked for the US:**
   `curl -s "https://www.youtube.com/watch?v=<ID>"` and look for `"availableCountries"`.
   If that list is present and does **not** include `"US"`, it's region-locked away from
   the US → drop it. If the list is absent (unrestricted) or includes `US`, it's good.
   Also drop anything whose page shows `"status":"ERROR"` / `UNPLAYABLE` / "Video unavailable".
3. If a clip fails, find another (prefer official channels). If you can't verify a US clip,
   fall back per Step 4 (broadcaster page, then recap). Never attach an unverified video.

## Step 6 — Write the day file: `docs/days/TODAY.json`
Schema:

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
  culture, f1, golf, tennis`. Culture items use `culture`. Any other value renders neutral.
- `tag` is the visible label, `Topic · Subtopic` (e.g. `Culture · Film`, `Culture · Music`,
  `Culture · TV`, `Tennis · Wimbledon`).
- `watch`: `▶ The clip everyone saw` for a real highlight/trailer, `▶ Worth a look` /
  `▶ Worth a listen` / `▶ Watch it live` otherwise.
- `links` `kind`: `yt` (verified YouTube, red), `tw` (Twitch, purple), `web` (source/
  broadcaster page, green). Put the clip first, then 1 source. Every `yt` link MUST have
  passed Step 5.

## Step 7 — Update `docs/manifest.json`
Prepend `TODAY` to the `days` array if not present (keep newest-first), update `updated`,
keep `repo`. Never delete old day files — the archive is the point.

## Step 8 — Commit & push
Commit `docs/days/TODAY.json` + `docs/manifest.json` with message `digest: TODAY` and push
to the default branch.

## Tone
Warm, casual, a little fun — these are ice-breakers, not a news report. Avoid politics.
