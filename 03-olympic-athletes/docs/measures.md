# DAX Measures

Every measure used in this report, with its definition and the reasoning behind it.

<!--
For each measure, record:
  - the DAX code
  - what business question it answers
  - why it is a measure rather than a calculated column
  - any filter context assumptions a reader should know about
-->

## Example format

### Total CO2

```dax
Total CO2 =
SUM ( 'emissions'[co2] )
```

Base additive measure. All other emissions measures build on this one.

---

### CO2 Per Capita

```dax
CO2 Per Capita =
DIVIDE (
    [Total CO2],
    SUM ( 'emissions'[population] )
)
```

Calculated as a ratio of sums rather than an average of a per-row column, so it
re-aggregates correctly when several countries are in filter context. `DIVIDE`
handles the division-by-zero case for years with no population data.
