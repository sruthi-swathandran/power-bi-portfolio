# Kickstarter Projects — Crowdfunding Success Analysis

An interactive report covering 374,853 Kickstarter campaigns launched between
2009 and 2017.

---

## Business questions

1. Which category has the highest success rate, and how many projects succeeded?
2. Among projects with a goal over $1,000, which achieved the highest goal
   completion percentage?
3. How have success rates trended over the years?
4. What kinds of projects should an investor be looking at?

---

## Data

**Source:** [Maven Analytics Data Playground](https://mavenanalytics.io/data-playground) — "Kickstarter Projects"
**Licence:** Public Domain
**Rows:** 374,853 · **Columns:** 11 · **Period:** 2009–2017

Place `kickstarter_projects.csv` in `data/`. It is not committed to this repository.

---

## Data quality: which projects count

The `State` column has five values:

| State | Projects |
|-------|---------:|
| Failed | 197,611 |
| Successful | 133,851 |
| Canceled | 38,751 |
| Live | 2,798 |
| Suspended | 1,842 |

The 2,798 `Live` projects had not yet concluded when the data was captured, so
they have no final outcome and are excluded from all success-rate calculations.
Including them understates the success rate by treating unfinished campaigns as
failures.

On that basis the overall success rate is **36.0%** (133,851 of 372,055 concluded
projects), not the 35.7% you get by dividing by the full row count.

---

## Model

<!-- Document the date table and any category dimension. -->

---

## Measures

Full definitions in [`docs/measures.md`](./docs/measures.md).

---

## Findings

<!-- Fill in as you build. -->

---

## Screenshots

<!-- Add to images/ and embed here. -->
