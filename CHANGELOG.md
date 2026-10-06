# Changelog

All notable changes to Derby-Agegroup. The same history is kept in the
`Changelog:` section of the `Derby-Agegroup.pyw` module docstring; the
version number is the `VERSION` constant in that file.

## 1.4.0

- Blank **Age Up Year** now defaults to the latest race year found among the
  directory's XML files, minus 1, instead of the current year minus 1. It
  falls back to the current year minus 1 if there's no directory or no
  readable race dates. A value typed into the field (or passed with
  `--age-up-year`) still takes precedence.
- The GUI's "(blank = ####)" hint now updates when the directory changes, not
  just when the field is edited.
- The PDF title/filename date is now the newest race date found across all of
  the directory's XML files. Every sex/class combination shares one date,
  rather than it being computed independently per sex.

## 1.3.0

- Added a **Use WC Points** checkbox (checked by default). When unchecked,
  each run is scored by its raw finish place (1st = 1, 2nd = 2, ...) instead
  of WC points, and "best" flips accordingly everywhere it matters: the
  Best of N Runs pick, the sort order, and RANK all then favor the lowest
  total instead of the highest. Column headers switch between "... Pts" and
  "... Place" to match.
- Added the matching CLI `--no-wc-points` flag. The checkbox state is saved to
  and restored from the JSON config.

## 1.2.0

- The date in the results PDF title and filename is now the latest race's
  actual `<Racedate>` from the XML data (computed independently per sex),
  not today's system date.
- Fixed a data bug where the same athlete could be split into two different
  ids and never combine into one row. `NAT_code` isn't consistently
  formatted even within one season -- some race files prefix it with a
  nationality letter (`E6480922`), others don't (`6480922`) -- and
  unconditionally stripping the first character either way corrupted the
  no-prefix case by chopping off a real digit. The leading character is now
  only stripped when it's actually a letter.

## 1.1.0

- Added a **Best of N Runs** column/field: sums the racer's N highest-scoring
  individual runs out of all runs across all races (blank = all runs).
- Added the matching GUI "Best of Runs:" field (with live validation/hint,
  saved to and restored from the JSON config) and CLI `--best-of-runs` flag.
- RANK is now based on the Best of Runs total instead of the raw points sum
  (identical when the field is left blank).
- Removed the separate WC POINTS/POINTS column from the CSV and PDF; Best of
  Runs is now the only points total shown.
- The PDF's "Best of Runs" header always breaks onto two lines ("Best of" /
  the count) rather than relying on automatic word-wrap.

## 1.0.0

- Initial release, forked from Derby-PACup.pyw v1.2.0: U16/U14/U12/U10 age
  classes, per-run (not per-race-total) WC points scoring.
- Same GUI/CLI feature set as Derby-PACup: directory picker, XML file tree,
  Age Up Year field, Exclude IDs/Clubs filters, class checkboxes, Generate
  CSV files toggle, Run button, Log tab, one PDF preview tab per
  (sex, class), JSON config persistence under `%APPDATA%\Derby-Agegroup\`,
  version number, About dialog.

## Unreleased

- CLI `--age-up-year` help text now describes the 1.4.0 default (latest race
  year in the XML files minus 1).
