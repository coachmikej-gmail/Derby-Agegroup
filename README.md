# Derby-Agegroup

Scores the U14/U12 age-group ski racing series from Vola/FIS race-results XML
files and produces standings as CSV and PDF. It runs as a GUI (no arguments)
or as a command-line tool (with arguments), from a single file:
`Derby-Agegroup.pyw`.

It is a Python port of the legacy `Derby-U14U12.tcl` scripts and is based on
[Derby-PACup](../Derby-PACup/). Current version: see `VERSION` in
`Derby-Agegroup.pyw` and [CHANGELOG.md](CHANGELOG.md).

## What it does

Point it at a directory of Vola/FIS results XML files. Every `*.xml` file is
treated as one race. The Raceheader `Gender` attribute (`M`/`L`, or the older
`Sex` attribute some seasons use) decides whether it is a Men's or Women's
race. The XML is parsed directly; no intermediate CSV is needed.

For each (sex, class) combination that had races, it writes:

- `Results-<Sex>-<Class>.csv` (can be turned off)
- `Age Group Standings <date>-<Sex>-<Class>.pdf`, a gridded, paginated
  standings sheet. Up to 8 PDFs if both sexes raced and all four classes are
  selected.

Race columns are labeled `<Eventname> @ <Place>` from the XML and ordered by
`Racedate`, earliest first, with the most recent race next to the total
column. The date in the PDF title and filename is the **newest race date
found among all the XML files**.

### Classes

Class membership is by age, where age = Age Up Year - birth year:

| Class | Age |
|-------|-------|
| U16   | 14-15 |
| U14   | 12-13 |
| U12   | 10-11 |
| U10   | 8-9   |

Racers with a missing or non-numeric birth year are kept in the log but
excluded from every class.

### Scoring

Points are scored **per run**, not by total race time. Each race's Run1 and
Run2 are ranked independently within each class, so a two-run race gives two
scoring opportunities.

- **Use WC Points** (default): each run scores World Cup points for its finish
  place (1st = 100, 2nd = 80, 3rd = 60, ... 30th = 1). Higher is better.
- Unchecked / `--no-wc-points`: each run scores its raw finish place (1st = 1,
  2nd = 2, ...). Lower is better, and RANK, sort order and the Best-of pick all
  flip to match.
- **Best of N Runs**: the total shown is the sum of the racer's N best
  individual runs across all races (not the N best races). Blank means all
  runs. RANK is based on this total.
- Ties share a rank, and the next distinct rank skips ahead by the tie count.

### Age Up Year

When left blank, the Age Up Year defaults to **the latest race year found in
the XML files, minus 1**. It falls back to the current year minus 1 if there's
no directory or no readable race dates. A value you type always takes
precedence. The GUI shows the effective value live as `(blank = ####)` /
`(using ####)`.

## Requirements

- Python 3 with Tkinter (included with the standard Windows installer)
- `reportlab` and `pymupdf`:

```
pip install reportlab pymupdf
```

## Usage

### GUI

```
python Derby-Agegroup.pyw
```

Or double-click `Derby-Agegroup.pyw`. Choose the directory of XML files, adjust
the options, and press **Run**.

- **Select Directory...** shows each XML file with its Racedate, Eventname and
  Place.
- **Age Up Year**, **Best of Runs**, **Exclude IDs**, **Exclude Clubs**
  (comma-separated; club matching is case-insensitive).
- Class checkboxes, **Generate CSV files**, **Use WC Points**.
- The **Log** tab shows progress. There is one PDF preview tab per
  (sex, class), with page navigation and an "Open in default viewer" button.
- Settings (including window position and last directory) are saved on exit to
  `%APPDATA%\Derby-Agegroup\Derby-Agegroup-GUI-config.json` and restored on the
  next launch.

### Command line

```
python Derby-Agegroup.pyw [directory] [options]
```

`directory` defaults to the current directory.

| Option | Meaning |
|--------|---------|
| `--age-up-year`, `-a YEAR` | Age Up Year; blank = latest race year in the XML files - 1 |
| `--exclude-ids ID,ID,...` | Drop these USSA ids from every output |
| `--exclude-clubs CLUB,...` | Drop these club codes from every output |
| `--classes U16,U14,...` | Classes to produce; blank = all four |
| `--no-csv` | Skip writing `Results-*.csv` (PDFs are always written) |
| `--best-of-runs N` | Total is the sum of the N best runs; blank = all runs |
| `--no-wc-points` | Score by raw finish place instead of WC points |
| `--version` | Print the version |

Example:

```
python Derby-Agegroup.pyw "Y:\Derby SW\U14U12\2024" --classes U14,U12 --best-of-runs 6
```

## Example data

`xml-example/` holds the eight Vola XML race files from the 2024 season (four
men's and four women's races, 2/3/2024 to 2/11/2024) so you can try the app.
Select that folder in the GUI, or run:

```
python Derby-Agegroup.pyw xml-example
```

With a blank Age Up Year this resolves to 2023. Output files are written into
`xml-example/`; the `.gitignore` keeps the generated CSVs and PDFs out of git.

## Notes

- A corrupted, empty or encrypted XML file (e.g. one mangled by antivirus
  software) is logged and skipped; the remaining files still process.
- Output files are written into the XML directory.

## License

MIT. See [LICENSE](LICENSE). Author: Michael Jacobson, coachmikej@gmail.com.
