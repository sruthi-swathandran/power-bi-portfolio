# DAX measures

Every measure in this report, with its definition and the reasoning behind it.
Twenty-four in total, grouped by what they answer.

All of them live in a dedicated `_Measures` table rather than being scattered
across the model, so a reader opening the file finds the whole calculation layer
in one place.

## A note on filter direction

Relationships run one-to-many from each dimension to `Entries`, with single
cross-filter direction. Filters travel from dimension to fact and not back.

That matters for any measure counting distinct values of a dimension column. A
plain `DISTINCTCOUNT(Event[Sport])` ignores filters applied to `Entries`, so
selecting Winter 2006 would still return every sport ever contested. Wrapping the
column in `SUMMARIZE(Entries, ...)` starts from the fact table, which is already
filtered, and reads the related dimension value for each surviving row.

Columns that sit on `Entries` itself need none of this, which is why `Countries`,
`Events Contested` and `Games Held` use a plain `DISTINCTCOUNT`.

---

## Counting the base entities

### Entries

```dax
Entries = COUNTROWS(Entries)
```

Row count of the fact table. One athlete in one event at one Games. Rarely shown
on a page, but it is the denominator behind several sanity checks and makes the
grain explicit to anyone reading the model.

### Athletes

```dax
Athletes = DISTINCTCOUNT(Entries[ID])
```

Distinct people in the current filter context. An athlete who entered six events
at one Games counts once, and an athlete who competed at four Games counts once
across all of them.

This does not sum across Games. Adding the 1960 and 1964 figures double-counts
anyone who went to both.

### Countries

```dax
Countries = DISTINCTCOUNT(Entries[NOC])
```

Distinct national committees. `NOC` is a column on `Entries`, so the filter
context reaches it directly.

### Games Held

```dax
Games Held = DISTINCTCOUNT(Entries[Games])
```

How many separate Games the current selection appears at. Used on the
discontinued sports table, where a sport appearing once and a sport appearing
seven times need telling apart.

### Events Contested

```dax
Events Contested = DISTINCTCOUNT(Entries[Event])
```

Distinct events actually held. Counting the `Event` dimension instead would
return the full 765 regardless of which Games is selected, since most events have
not been held at most Games.

### Sports

```dax
Sports = COUNTROWS(SUMMARIZE(Entries, Event[Sport]))
```

The exception described above. `Sport` exists only on the `Event` dimension,
never on `Entries`, so this is the one count that needs `SUMMARIZE` to respect a
filter on the fact table.

---

## Medals

### Medals

```dax
Medals = DISTINCTCOUNTNOBLANK(Entries[MedalKey])
```

The central measure of the report. `MedalKey` combines Games, event, medal type
and NOC, and is blank for entries that did not medal.

The source has one row per athlete, so a team gold produces a row per player.
Counting rows gives 39,772 medals across the data. Counting distinct keys gives
18,905. Using the wrong one inflates every team-sport nation.

`DISTINCTCOUNTNOBLANK` rather than `DISTINCTCOUNT` because the latter counts
blank as a value, which would add one phantom medal to every non-empty filter
context.

### Medal Rows

```dax
Medal Rows = CALCULATE(COUNTROWS(Entries), NOT ISBLANK(Entries[Medal]))
```

The count that `Medals` deliberately avoids: medal-winning entries, which is
close to a count of medallists. It exists so the gap between the two can be shown
on the page rather than asserted in a footnote.

### Rows per Medal

```dax
Rows per Medal = DIVIDE([Medal Rows], [Medals])
```

The ratio between them, which is roughly a country's average medal-winning team
size. It runs at 2.1 across the whole dataset and reaches 4.9 for Serbia, whose
record is concentrated in team sports.

`DIVIDE` rather than `/` so countries with no medals return blank instead of an
error.

### Gold, Silver, Bronze

```dax
Gold = CALCULATE([Medals], Entries[Medal] = "Gold")
```

```dax
Silver = CALCULATE([Medals], Entries[Medal] = "Silver")
```

```dax
Bronze = CALCULATE([Medals], Entries[Medal] = "Bronze")
```

Each builds on `Medals` rather than repeating the distinct count, so a change to
how a medal is identified propagates to all three.

---

## Rates

### Medals per 100 Athletes

```dax
Medals per 100 Athletes = DIVIDE([Medals], [Athletes]) * 100
```

Medals won for every hundred competitors sent. This reorders the medal table
substantially: Ethiopia sits near the top on 232 athletes while holding 53
medals.

The rate is unstable on small delegations, so every visual using it carries a
filter of 200 or more athletes. Without that floor the ranking fills with
countries that sent one competitor who medalled.

The filter belongs on `Athletes`, not on this measure. Filtering
`Medals per 100 Athletes >= 200` looks similar and empties the chart, which
happened during development.

### Female Athletes

```dax
Female Athletes = CALCULATE([Athletes], Athlete[Sex] = "F")
```

### Female Share

```dax
Female Share = DIVIDE([Female Athletes], [Athletes])
```

Share of distinct people rather than of entries, so a woman entering five events
does not count five times. `Sex` sits on the `Athlete` dimension, and filtering a
dimension inside `CALCULATE` works in the normal direction, so no `SUMMARIZE` is
needed here.

---

## Physique and age

### Median Age

```dax
Median Age = MEDIAN(Entries[Age])
```

Median rather than mean, because age distributions in several sports are skewed
by a small number of much older competitors. Equestrian events in particular have
medallists in their fifties and sixties.

Entries with no recorded age drop out rather than being imputed.

### Avg Height cm, Avg Weight kg

```dax
Avg Height cm = AVERAGE(Entries[Height])
```

```dax
Avg Weight kg = AVERAGE(Entries[Weight])
```

Mean is defensible here because height and weight are close to symmetric within a
sport, unlike age.

Both columns are sparse before 1960, recorded for between 12% and 31% of
athletes, against 94% or more afterwards. Every visual using them starts at 1960
and the page says so.

### Avg Height Women, Avg Height Men

```dax
Avg Height Women = CALCULATE(AVERAGE(Entries[Height]), Athlete[Sex] = "F")
```

```dax
Avg Height Men = CALCULATE(AVERAGE(Entries[Height]), Athlete[Sex] = "M")
```

These exist because the combined average is misleading on its own. Between 1960
and 2016 men gained 6.1 cm and women gained 6.2 cm, while the combined average
gained 2.7 cm. The share of women rose from 11.5% to 45.0% over the same period,
and since women are shorter on average, a growing share of them holds the
combined figure down while both groups rise.

Showing only `Avg Height cm` would understate the change by more than half. The
three measures sit on one chart for that reason.

---

## Host advantage

### Is Host

```dax
Is Host = IF(Entries[NOC] = RELATED(Games[host_noc]), "Host", "Visitor")
```

A calculated column on `Entries`, not a measure. It has to be evaluated per row
before anything can filter on it.

`host_noc` comes from `reference/host_countries.csv`, merged into the `Games`
query rather than added as a sixth table. A separate table would have put two
relationship hops between `Entries` and the host country, and `RELATED` only
crosses one.

### Host Medals

```dax
Host Medals = CALCULATE([Medals], Entries[Is Host] = "Host")
```

### Host Medal Share

```dax
Host Medal Share = DIVIDE([Host Medals], [Medals])
```

The host country's share of all medals awarded at that Games. It reached 82.4% at
St Louis in 1904 and has a median of 6.5% since 1960.

This measure is bounded in a way worth stating. As the number of competing
countries grows, any single country's share falls even if its performance is
unchanged, so the decline over time is partly arithmetic. It answers "how much of
this Games did the host win" and not "did hosting help", which would need a
comparison against the same country's away performance.

---

## Programme history

### First Year, Last Year

```dax
First Year = MINX(Entries, RELATED(Games[Year]))
```

```dax
Last Year = MAXX(Entries, RELATED(Games[Year]))
```

Earliest and latest Games at which the current selection appears, used on the
discontinued sports table.

`MINX` iterates the filtered `Entries` rows and reads each row's year through the
relationship. `MIN(Games[Year])` would return the same value for every sport,
because a measure over a dimension column does not see the fact table's filters.

These are minimums and maximums, so they are not additive. Neither is
`Games Held`, and neither is any distinct count above. A total row on a table of
these recalculates over the whole filtered set instead of adding the column, which
produces a number that looks checkable and is not. Totals are switched off on the
tables that use them.
