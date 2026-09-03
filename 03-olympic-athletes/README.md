# Olympic Athletes — 120 Years of the Modern Games

An interactive report covering every athlete entry at the modern Olympic Games
from Athens 1896 to Rio 2016.

---

## Business questions

1. How has the share of female athletes changed over time?
2. How do the Summer and Winter Games compare on athletes, countries, and events?
3. Which countries send the most athletes — and do they also win the most medals?
4. How have these country-level trends shifted across the decades?

---

## Data

**Source:** [Maven Analytics Data Playground](https://mavenanalytics.io/data-playground) — "120 Years of Olympic History"
(originally Sports Reference)
**Licence:** Public Domain
**Rows:** 271,116 athlete entries · 230 country codes

Files: `athlete_events.csv`, `country_definitions.csv`. Place both in `data/`.
They are not committed to this repository.

---

## Data quality: two fixes required before loading

**1. `country_definitions.csv` uses CR-only line endings.**

The file is saved in the legacy Mac format with carriage returns and no line
feeds. Power Query reads the entire file as a single row. Convert it to CRLF
before loading — the conversion step is documented in `docs/data-prep.md`.

**2. Missing values are the literal string `NA`.**

`Age`, `Height`, `Weight`, and `Medal` use `NA` rather than an empty cell. Left
untreated, Power Query types all four columns as text, making age and height
unusable in any calculation. These are replaced with nulls during import.

Note that `NA` in `Medal` is meaningful — it indicates the athlete did not medal,
which is the majority of entries. It should become a null, not be filtered out.

---

## Model

<!-- Document the star schema: athlete events fact, country dimension, games dimension. -->

---

## Measures

Full definitions in [`docs/measures.md`](./docs/measures.md).

---

## Findings

<!-- Fill in as you build. -->

---

## Screenshots

<!-- Add to images/ and embed here. -->
