# Global CO2 Emissions — 270 Years of Fossil Fuel Data

An interactive report covering global carbon dioxide emissions from 1750 to 2021,
by country, alongside population data for the same year and country.

---

## Business questions

1. How have global CO2 emissions changed over time?
2. Which countries emit the most each year, and how has that ranking shifted?
3. Where does the *average person* emit the most CO2 — that is, who leads on a
   per-capita basis rather than in absolute terms?
4. How do emissions from fossil fuels compare with those from land use change?

---

## Data

**Source:** [Our World in Data — CO2 and Greenhouse Gas Emissions](https://ourworldindata.org/co2-emissions)
**Licence:** CC BY 4.0
**Period:** 1750–2021
**Rows:** 50,598 · **Columns:** 79

Download and place `visualizing_global_co2_data.csv` in `data/`. It is not
committed to this repository.

---

## Data quality: two decisions that change the answer

**1. The `country` column contains aggregates, not just countries.**

Alongside real countries, the column includes `World`, `Africa`, `Asia`, `Europe`,
`North America`, `South America`, `Oceania`, `High-income countries`,
`Lower-middle-income countries`, `Upper-middle-income countries`,
`International transport`, and several `(GCP)` regional variants.

Left unfiltered, these break the analysis in two ways: `World` tops every
"largest emitter" ranking, and each continent double-counts its own member
countries in any total. Aggregate rows are identified by a blank `iso_code` and
are excluded from all country-level visuals.

**2. Per-capita emissions must be a measure, not a column.**

Averaging a pre-calculated per-capita column across countries gives every country
equal weight regardless of population, which is wrong. Per-capita figures are
calculated as `DIVIDE(SUM(co2), SUM(population))` so they re-aggregate correctly
at any level of the report.

---

## Model

<!-- Document the star schema: fact table, country dimension, date table. -->

---

## Measures

Full definitions in [`docs/measures.md`](./docs/measures.md).

---

## Findings

<!-- Fill in as you build. Lead with what the data says, then show the visual. -->

---

## Screenshots

<!-- Add to images/ and embed here: ![Overview](./images/overview.png) -->

---

## Opening the report

Requires Power BI Desktop with PBIP preview enabled. See the
[repository README](../README.md) for setup steps.
