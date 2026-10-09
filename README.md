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
| 01 | [Global CO2 emissions](./01-global-co2-emissions) | Climate | 50,598 rows, 1750 to 2021 | **Complete** |
| 02 | [Kickstarter projects](./02-kickstarter-projects) | Crowdfunding | 374,853 rows | **Complete** |
| 03 | [Olympic athletes](./03-olympic-athletes) | Sport | 271,116 rows normalised into 5 tables | **Complete** |

### Global CO2 emissions

Five pages on 270 years of emissions data, built around three questions that
turn out to be one argument. Some of what it found:

- More than half of all CO2 ever emitted was released after 1990. The halfway point of 270 years of industrialisation falls in 1993
- China accounts for 14% of all emissions ever and 31% of today's. The United States, 24% and 13%
- The United States cut production emissions 8% since 1990 and increased consumption emissions 3%. A third of Britain's larger reduction is imports rather than anything that happened in Britain
- Twenty-seven countries grew their economies while cutting emissions, and six of them are post-Soviet states whose decline was over by 1998
- Coal's share of emissions fell from 100% to 40% while its volume almost quadrupled

Full write-up in the [project README](./01-global-co2-emissions).

### Kickstarter projects

Four pages covering platform growth, category performance, goal setting, and
timing. Some of what it found:

- Success rate falls from 55% to 10% as goals rise, without a single reversal across seven bands
- Failure is rarely a near miss. Successful campaigns finish at a median 117% of goal, failed ones at 2%
- Campaigns accepting Kickstarter's preset 30-day duration succeed 35.7% of the time; campaigns one day longer succeed 55.1%
- The categories raising the most money succeed least often

Full write-up, screenshots and assumptions in the
[project README](./02-kickstarter-projects).

### Olympic athletes

Six pages on 120 years of the Games, from a flat file normalised into a
five-table star schema. Some of what it found:

- Counting medal rows instead of medals overstates the total by 2.1 times, and by 4.9 times for Serbia
- 149 of 230 national committees have ever won a medal, and the top ten hold 59.9% of every medal awarded since 1896
- Men and women each gained about six centimetres between 1960 and 2016 while the combined average gained under three, because the share of women rose at the same time
- The host country won 82.4% of the medals at St Louis in 1904. Since 1960 the median host takes 6.5%
- The Olympics awarded medals for art at seven Games between 1912 and 1948

Full write-up in the [project README](./03-olympic-athletes).

---

## Techniques used

**Data modelling.** Star schema with a dedicated date table, one-to-many
relationships with single cross-filter direction, and Power BI's automatic
date/time hierarchies disabled so the model carries no hidden calendar tables.
One project normalises a single flat file into five tables, with the source
loaded once as a staging query and every dimension built as a reference from it.

**Power Query.** Type enforcement, conditional columns for banding, merges
against hand-built reference data, and derived row-level columns computed at
refresh rather than as measures.

**DAX.** `CALCULATE` for conditional aggregation, `DIVIDE` for safe division,
`MEDIAN` and `MEDIANX` where distributions are skewed enough that averages
mislead, and denominators named as their own measures so the definitional choices
behind a rate stay visible. `SUMMARIZE` where a count over a dimension column
would otherwise ignore the fact table's filters, and calculated columns only
where a value has to exist per row before anything can filter on it. `TOPN`
inside a measure where a ranking has to recalculate per year rather than be
fixed at today's list, and `VAR` where a measure has to be resolved before
`CALCULATE` can filter on it.

**What-if parameters.** A numeric range driving a baseline year, so a reader can
move the comparison point and watch which countries drop out of a finding. A
result that survives being poked at is worth more than one presented as settled.

**Deciding what a ratio is.** Source files routinely ship pre-calculated
per-capita and share-of-total columns. None of them can be summed or averaged,
so all of them are deleted from the model and rebuilt as measures. Averaging
Qatar's 35.6 tonnes per person and China's 8.0 gives 21.8, which describes
nobody.

**Counting at the right grain.** Source files rarely arrive at the grain a
question needs. Distinguishing medals from medal-winning entries changes the
Olympic totals by a factor of 2.1 overall and 4.9 for one country, and the report
shows both counts rather than picking one quietly.

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
