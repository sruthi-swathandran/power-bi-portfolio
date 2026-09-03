# Power BI Portfolio

Three interactive dashboards built in Power BI Desktop, covering data modelling,
DAX measure design, and report layout.

All reports are saved in **PBIP format** (Power BI Project) rather than `.pbix`.
PBIP stores the report and semantic model as plain text and JSON, so the data
model, relationships, and every DAX measure are readable directly in this
repository and diffable in git.

---

## Projects

| # | Project | Domain | Scale | Focus |
|---|---------|--------|-------|-------|
| 01 | [Global CO2 Emissions](./01-global-co2-emissions) | Climate | 50,598 rows · 1750–2021 | Per-capita measures, filled maps, long-range time series |
| 02 | [Kickstarter Projects](./02-kickstarter-projects) | Business / crowdfunding | 374,853 rows | Success-rate KPIs, category benchmarking, YoY trends |
| 03 | [Olympic Athletes](./03-olympic-athletes) | Sport | 271,116 rows across 2 tables | Star-schema modelling, participation trends, medal analysis |

*Status: in progress.*

---

## Techniques demonstrated

- **Data modelling** — star schema design, relationship cardinality, filter direction
- **Power Query** — column pruning, type enforcement, null handling, unpivoting
- **DAX** — measures vs. calculated columns, `CALCULATE`, `DIVIDE`, time intelligence,
  iterators, and variables for readability
- **Date tables** — marked date tables supporting proper period comparison
- **Report design** — layout hierarchy, consistent colour use, accessible contrast,
  tooltips, drill-through, and bookmarks
- **Data quality documentation** — every exclusion and assumption stated in the report

---

## Repository structure

Each project folder follows the same layout:

```
NN-project-name/
├── README.md          Business questions, findings, and method
├── report/            PBIP project files (report + semantic model as text)
├── docs/              Measure definitions and data dictionary
├── images/            Dashboard screenshots
└── data/              Raw source files (gitignored — see project README)
```

Screenshots live in `images/` because `.pbip` files cannot be previewed on
GitHub — the images are how a reader sees the work without opening Power BI.

---

## Opening these reports

1. Install Power BI Desktop (free, Windows only).
2. Enable PBIP support if it is not already on:
   **File → Options and settings → Options → Preview features →
   "Power BI Project (.pbip) save option"**, then restart.
3. Clone this repository.
4. Download the source data using the link in the relevant project README, and
   place the files in that project's `data/` folder.
5. Open the `.pbip` file in that project's `report/` folder and refresh.

Raw data files are not committed — they are large and freely available from the
original sources. Every project README documents exactly where to get them.

---

## Tech stack

Power BI Desktop · DAX · Power Query (M) · Git

---

## About

Built by Sruthi. Feedback and questions are welcome via the issues tab.
