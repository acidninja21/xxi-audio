# How to refresh the XXI fin. trending audio list

This repo is a phone page (index.html) that loads `tracks.json`. A scheduled Claude task refreshes `tracks.json` twice a day. Only edit `tracks.json` — never index.html — unless Finn asks.

## 1. Gather current charts (WebFetch / WebSearch)
Fetch every page below. Skip any that fails; never invent entries.
- https://tokchart.com/tiktok-trending-songs/australia
- https://tokchart.com/tiktok-trending-sounds/australia
- https://tokchart.com/tiktok-trending-songs/united-states
- https://tokchart.com/tiktok-trending-sounds/united-states
- https://tokchart.com/tiktok-trending-songs/canada
- https://tokchart.com/tiktok-trending-songs  (global)
- https://tokchart.com/  (global sounds)
- https://soundcharts.com/en/charts/tiktok/worldwide  (≈95 global songs)
- https://metricool.com/tiktok-songs/
- https://www.socialpilot.co/blog/tiktok-trends  (sounds + trend formats)
- https://www.revid.ai/viral-tiktok-songs
- One WebSearch: "trending TikTok sounds this week" for any fresh round-up from the last 7 days.

"Original audio" sounds only go in when the source names the song they use (e.g. Krissy's PNW = Pink Floyd "Time").

## 2. Build the list
- Start from the current `tracks.json`. Keep every track still on a chart; update its `src`/`stat`.
- Every track found on a source this run gets `"seen": "YYYY-MM-DD"` (today, Perth). A track with no `seen` counts as seen on 2026-11-19.
- Drop tracks whose `seen` is more than 7 days old (keep at most ~150 total).
- Add new tracks with `"new": 1`. Remove `new` from tracks that were already in the file.
- `hot: 1` for the ~15 biggest (highest videos/day or #1–3 on AU/US/Global).
- Order: hottest first (by videos/day, AU charts break ties), then the rest by chart rank.

## 3. Track format
```
{"t":"Title","a":"Artist","s":"<style>","src":"AU #2 · US #4","stat":"~47K new videos a day",
 "hot":1,"new":1,"moods":["hype","playful"],"e":4,
 "vibe":"Bold, glam, main-character pop.","idea":"Outfit transformation…","trend":"Optional: the TikTok trend format, only if a source describes one"}
```
- `s` (style) — exactly one of: Pop, Hip-Hop / Rap, R&B / Soul, Electronic / House, Phonk / Funk, Latin, K-Pop, Indie / Acoustic, Piano / Cinematic, Jazz / Lounge, Slowed & Sped up, Rock, Country, World, Christmas.
- `moods` — 1–3 of: hype, motivational, chill, sad, romantic, luxury, playful.
- `e` — energy 1 (very calm) to 5 (max).
- `vibe` — under 8 words describing the sound. `idea` — one short filming idea Finn (Perth, self-improvement/business creator, also films for local businesses) could shoot. Keep both under ~14 words.
- `stat` — the page reads numbers from it, so keep these exact formats: `~47K new videos a day`, `~46K a day · up 30%`, `Up 72% this week`, `1M videos`. Omit if the source gives no number.
- `stage` — optional, `"Rising"` or `"Peak"`, only when a source says so (e.g. SocialPilot's Stage). Otherwise leave it out; the page works it out.
- `trend` — fill it whenever any source describes how creators use the sound (format, challenge, POV). This is the most useful field for Finn, so check SocialPilot, NewEngen and Metricool for it.
- Skip songs you can't describe confidently.

Top level: `{"updated": ISO time in Australia/Perth, "updated_label": "19 Nov, 4:30pm", "sources": [...], "tracks": [...]}`.

## 4. Save and publish
Validate the JSON (`python3 -m json.tool tracks.json`), then commit to main with message `Refresh trending audio — <date time>` and push. GitHub Pages redeploys automatically.
