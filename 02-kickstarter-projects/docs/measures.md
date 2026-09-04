# DAX measures

Every measure in the report, with its definition and the reasoning behind it.
All eleven live in a dedicated `Key Measures` table so they sit together at the
top of the field list rather than attaching to whichever table happened to be
selected when they were created.

---

## Counting

### Total Projects

```dax
Total Projects = COUNTROWS(Projects)
```

Base count. Every other count builds on this one, so a change to what a project
means happens in a single place.

### Successful Projects

```dax
Successful Projects =
CALCULATE([Total Projects], Projects[State] = "Successful")
```

`CALCULATE` re-evaluates the base measure with an extra filter applied. This is
the DAX equivalent of a conditional aggregation in SQL.

### Resolved Projects

```dax
Resolved Projects =
CALCULATE([Total Projects], Projects[State] IN { "Successful", "Failed" })
```

The denominator for every success rate in the report, and the most consequential
decision in the model.

`State` has five values. Cancelled campaigns were withdrawn by their creator and
Live ones had not finished when the data was captured, so neither has an outcome
that success can be measured against. Suspended campaigns were removed by the
platform.

Including all five gives 35.7%. Excluding only Live gives 36.0%. Excluding
everything unresolved gives 40.4%. None of those is wrong, but a measure named
`Success Rate` that silently picks one hides the choice from anyone reading the
file. Naming the denominator as its own measure puts it in the field list where
it can be questioned.

---

## Rates

### Success Rate

```dax
Success Rate = DIVIDE([Successful Projects], [Resolved Projects])
```

`DIVIDE` rather than `/` because it returns blank instead of an error when a
filter leaves no resolved projects in context, which happens as soon as a user
clicks a slicer combination with no data behind it.

### Median Pct of Goal

```dax
Median Pct of Goal =
MEDIANX(Projects, DIVIDE(Projects[Pledged], Projects[Goal]))
```

`MEDIANX` iterates the table, computes each project's ratio, then takes the median
of those ratios. That is deliberately not `SUM(Pledged) / SUM(Goal)`, which would
be dominated by the largest campaigns: goals run to $166 million while the median
is $5,500.

The inner `DIVIDE` handles the four projects with a goal of $0, returning blank
rather than an error.

---

## Money and support

### Total Pledged

```dax
Total Pledged = SUM(Projects[Pledged])
```

### Total Backings

```dax
Total Backings = SUM(Projects[Backers])
```

Named backings rather than backers on purpose. The source column counts backers
per project, so summing it counts one person once per project they supported. The
dataset contains no identifier that would allow distinct people to be counted, and
a measure called `Total Backers` would imply a claim the data cannot support.

### Backings per Project

```dax
Backings per Project = DIVIDE([Total Backings], [Total Projects])
```

Support per campaign. This is the measure that turns the correlation on the
Overview page into a mechanism: in 2014 project count rose 49% while total
backings fell, so support per project dropped by a third.

### Avg Pledge per Backing

```dax
Avg Pledge per Backing = DIVIDE([Total Pledged], [Total Backings])
```

Sum over sum, not the average of each project's ratio. Dividing totals weights
every backing equally and re-aggregates correctly when the report is filtered to
one category. Averaging per-project ratios would give a campaign with three
backers the same weight as one with 200,000.

Roughly 51,800 projects have zero backings, which is why `DIVIDE` is required
rather than plain division.

---

## Distribution

### Median Goal

```dax
Median Goal = MEDIAN(Projects[Goal])
```

### Median Pledged

```dax
Median Pledged = MEDIAN(Projects[Pledged])
```

Median rather than average throughout, because both distributions are severely
skewed. Median goal is $5,500 while the mean is $45,864, pulled by a single
campaign asking for $166 million. An average here would describe a project that
does not exist.

Note that the storage type does not constrain this. `Goal`, `Pledged` and
`Backers` are all Whole Number columns, since the source contains no fractional
values in any of the 374,853 rows, and aggregations over them still return
decimals.

---

## Why these are measures and not columns

Anything computed per row and independent of filter context is a Power Query
column: `Launch Date`, `Duration Days`, `Goal Band`. They are computed once at
refresh and stored.

Anything that aggregates across rows is a measure, computed on demand against
whatever the user has filtered. Storing an aggregate as a column would freeze it
at one grain and break the moment someone clicked a slicer.
