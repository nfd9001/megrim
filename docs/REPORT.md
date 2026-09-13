# Exporting a shareable report (HTML + PDF)

`tools/report.html` is a **single-file, fully offline static webapp** that turns a Megrim
`megrim-export` v1 JSON file into a clean, printable report you can hand to a clinician or
specialist — through an upload portal, on a USB stick, or on a laptop with no internet at all.

## Privacy model

The page makes **zero network requests** and **stores nothing**: no CDN scripts, no web fonts,
no analytics, no cookies, no localStorage. Your export is parsed in memory inside the tab and
vanishes when the tab closes. Verify by opening your browser's network inspector while using it,
or by loading it with Wi-Fi turned off.

What it does *not* contain: the full daily pressure history, so the app's pressure-change
correlation is omitted (everything else about the odds-ratio analysis is reproduced faithfully —
migraine-days, study window, Haldane–Anscombe correction, the same buckets and display gates).

## Use

1. Copy `tools/report.html` anywhere (it is self-contained; no server needed).
2. Open it in any modern browser (`file://` is fine).
3. Drop in your export (Megrim app → Settings → Export → JSON).
4. **HTML:** you're already looking at it — hand over the laptop, or right-click → Save Page As.
5. **PDF:** click **Print / Save as PDF** (or Ctrl/Cmd-P) and choose "Save as PDF" as the
   destination. The print stylesheet paginates the report; tables repeat their headers and
   sections start on fresh pages.

## What it renders

- Summary cards (events, migraine-days, study window, median severity/duration, most-reported
  trigger, aura count)
- Events-per-month timeline (bar color = average severity), severity distribution
- Patterns: weekday, time of day, season (hemisphere-corrected), moon phase, daylight length,
  duration histogram, sleep and stress summaries, self-reported trigger/food/head-location
  tallies
- Medication table with helped / didn't-help / unknown outcomes
- **Suspected factors**: odds ratios over migraine-days, computed exactly like the app
  (`correlations.dart`: ≥3 migraine-days per bucket, OR > 1, strongest 8 shown) — with the same
  read-this-first caveats
- Full event log, newest first

## Notes

- Timestamps with an explicit offset are converted to *your* browser's local time; bare local
  timestamps are read as local time (same rule as the app's importer).
- The report re-derives weekday/season/time-of-day from the raw timestamps and computes moon
  phase and daylight astronomically, so it works for external imports that omit the `derived`
  block. A latitude is needed for moon/daylight/season buckets; the export's `settings.home_location`
  is used when present, otherwise the median of per-event `geo_lat`.
- Malformed input produces a warning banner rather than a broken page; unreadable events are
  skipped and listed.