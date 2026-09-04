# Power BI portfolio

Three interactive reports built in Power BI Desktop, covering data modelling, DAX
measure design, and report layout.

All reports are saved in **PBIP format** rather than `.pbix`. PBIP stores the
report and semantic model as plain text, so the model, the relationships and every
DAX measure are readable directly in this repository and diffable in git. A
`.pbix` is a binary archive that nobody can inspect without downloading it and
owning Power BI.

The semantic models use **TMDL** and the reports use **PBIR**, which split those
text files further: one file per table, one file per visual. A commit then shows
which measure or which chart changed rather than reporting that one large JSON
file moved.

---

## Projects

| # | Project | Domain | Scale | Status |
|---|---------|--------|-------|--------|
| 01 | [Global CO2 emissions](./01-global-co2-emissions) | Climate | 50,598 rows, 1750 to 2021 | Not started |
| 02 | [Kickstarter projects](./02-kickstarter-projects) | Crowdfunding | 374,853 rows | **Complete** |
| 03 | [Olympic athletes](./03-olympic-athletes) | Sport | 271,116 rows across 2 tables | Not started |

### Kickstarter projects

Four pages covering platform growth, category performance, goal setting, and
timing. Some of what it found:

- Success rate falls from 55% to 10% as goals rise, without a single reversal across seven bands
- Failure is rarely a near miss. Successful campaigns finish at a median 117% of goal, failed ones at 2%
- Campaigns accepting Kickstarter's preset 30-day duration succeed 35.7% of the time; campaigns one day longer succeed 55.1%
- The categories raising the most money succeed least often

Full write-up, screenshots and assumptions in the
[project README](./02-kickstarter-projects).

---

## Techniques used

**Data modelling.** Star schema with a dedicated date table, one-to-many
relationships with single cross-filter direction, and Power BI's automatic
date/time hierarchies disabled so the model carries no hidden calendar tables.

**Power Query.** Type enforcement, conditional columns for banding, and derived
row-level columns computed at refresh rather than as measures.

**DAX.** `CALCULATE` for conditional aggregation, `DIVIDE` for safe division,
`MEDIAN` and `MEDIANX` where distributions are skewed enough that averages
mislead, and denominators named as their own measures so the definitional choices
behind a rate stay visible.

**Report design.** Titles that state the finding rather than describe the axes,
axes starting at zero, and partial periods excluded from trend charts with the
exclusion noted on the page.

**Documentation.** Every measure explained in `docs/measures.md`, every exclusion
and confound stated in the project README.

---

## Repository structure

Each project folder follows the same layout:

```
NN-project-name/
├── README.md          Business questions, findings, and method
├── report/            PBIP project files, report and semantic model as text
├── docs/              Measure definitions and data dictionary
├── images/            Report screenshots
└── data/              Raw source files, gitignored, see project README
```

Screenshots live in `images/` because `.pbip` files cannot be previewed on GitHub.
They are how a reader sees the work without opening Power BI.

---

## Opening these reports

1. Install Power BI Desktop, which is free and Windows only.
2. Clone this repository.
3. Download the source data using the link in the relevant project README and put
   the files in that project's `data/` folder.
4. Open the `.pbip` file in that project's `report/` folder and refresh.

If the `.pbip` option is missing when saving your own work, enable it under
File > Options and settings > Options > Preview features, then restart.

Raw data files are not committed. They are large and freely available from the
original sources, and every project README documents where to get them.

---

## Tech stack

Power BI Desktop · DAX · Power Query (M) · Git

---

## About

Built by Sruthi. Feedback and questions are welcome via the issues tab.
