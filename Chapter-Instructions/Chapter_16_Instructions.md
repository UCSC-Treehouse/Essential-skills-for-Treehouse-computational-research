# Chapter 16, Factors


Factors are used for categorical variables, variables that have a fixed
and known set of possible values. They are also useful when you want to
display character vectors in a non-alphabetical order.

We’ll start by motivating why factors are needed for data analysis and
how you can create them with factor(). We’ll then introduce you to the
gss_cat dataset which contains a bunch of categorical variables to
experiment with. You’ll then use that dataset to practice modifying the
order and values of factors, before we finish up with a discussion of
ordered factors.

------------------------------------------------------------------------

## Prerequisites

Base R provides some basic tools for creating and manipulating factors.
We’ll supplement these with the forcats package, which is part of the
core tidyverse. It provides tools for dealing with categorical variables
(and it’s an anagram of factors!) using a wide range of helpers for
working with factors.

``` r
library(tidyverse)
```

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ✔ ggplot2   3.5.2     ✔ tibble    3.2.1
    ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ✔ purrr     1.0.4     
    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

------------------------------------------------------------------------

## Factor Basics

Imagine that you have a variable that records month:

``` r
x1 <- c("Dec", "Apr", "Jan", "Mar")
```

Using a string to record this variable has two problems:

1.  There are only twelve possible months, and there’s nothing saving
    you from typos:

``` r
x2 <- c("Dec", "Apr", "Jam", "Mar")
```

2.  It doesn’t sort in a useful way:

``` r
sort(x1)
```

You can fix both of these problems with a factor. To create a factor you
must start by creating a list of the valid **levels**:

``` r
month_levels <- c(
  "Jan", "Feb", "Mar", "Apr", "May", "Jun",
  "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"
)
```

Now you can create a factor:

``` r
y1 <- factor(x1, levels = month_levels)
y1

sort(y1)
```

And any values not in the level will be silently converted to NA:

``` r
y2 <- factor(x2, levels = month_levels)
y2
```

This seems risky, so you might want to use forcats::fct() instead:

``` r
y2 <- fct(x2, levels = month_levels)
```

If you omit the levels, they’ll be taken from the data in alphabetical
order:

``` r
factor(x1)
```

Sorting alphabetically is slightly risky because not every computer will
sort strings in the same way. So forcats::fct() orders by first
appearance:

``` r
fct(x1)
```

If you ever need to access the set of valid levels directly, you can do
so with levels():

``` r
levels(y2)
```

You can also create a factor when reading your data with readr with
col_factor():

``` r
csv <- "
month,value
Jan,12
Feb,56
Mar,12"

df <- read_csv(csv, col_types = cols(month = col_factor(month_levels)))
df$month
```

------------------------------------------------------------------------

## General Social Survey

For the rest of this chapter, we’re going to use forcats::gss_cat. It’s
a sample of data from the General Social Survey, a long-running US
survey conducted by the independent research organization NORC at the
University of Chicago. gss_cat has been selected to illustrate some
common challenges with factors.

``` r
gss_cat
```

When factors are stored in a tibble, you can’t see their levels so
easily. One way to view them is with count():

``` r
gss_cat |>
  count(race)
```

------------------------------------------------------------------------

## Exercises pt 1 of 3

1.  Explore the distribution of rincome (reported income). What makes
    the default bar chart hard to understand? How could you improve the
    plot?

2.  What is the most common relig in this survey? What’s the most common
    partyid?

3.  Which relig does denom (denomination) apply to? How can you find out
    with a table? How can you find out with a visualization?

------------------------------------------------------------------------

## Modifying Factor Order

It’s often useful to change the order of the factor levels in a
visualization. For example, imagine you want to explore the average
number of hours spent watching TV per day across religions:

``` r
relig_summary <- gss_cat |>
  group_by(relig) |>
  summarize(
    tvhours = mean(tvhours, na.rm = TRUE),
    n = n()
  )

ggplot(relig_summary, aes(x = tvhours, y = relig)) +
  geom_point()
```

It is hard to read this plot because there’s no overall pattern. We can
improve it by reordering the levels of relig using fct_reorder().
fct_reorder() takes three arguments:

- “.f”, the factor whose levels you want to modify.
- “.x”, a numeric vector that you want to use to reorder the levels.
- Optionally, “.fun”, a function that’s used if there are multiple
  values of “.x” for each value of “.f”. The default value is median.

``` r
ggplot(relig_summary, aes(x = tvhours, y = fct_reorder(relig, tvhours))) +
  geom_point()
```

As you start making more complicated transformations, we recommend
moving them out of aes() and into a separate mutate() step. For example,
you could rewrite the plot above as:

``` r
relig_summary |>
  mutate(
    relig = fct_reorder(relig, tvhours)
  ) |>
  ggplot(aes(x = tvhours, y = relig)) +
  geom_point()
```

What if we create a similar plot looking at how average age varies
across reported income level?

``` r
rincome_summary <- gss_cat |>
  group_by(rincome) |>
  summarize(
    age = mean(age, na.rm = TRUE),
    n = n()
  )

ggplot(rincome_summary, aes(x = age, y = fct_reorder(rincome, age))) +
  geom_point()
```

Here, arbitrarily reordering the levels isn’t a good idea! That’s
because rincome already has a principled order that we shouldn’t mess
with. Reserve fct_reorder() for factors whose levels are arbitrarily
ordered.

However, it does make sense to pull “Not applicable” to the front with
the other special levels. You can use fct_relevel(). It takes a factor,
.f, and then any number of levels that you want to move to the front of
the line.

``` r
ggplot(rincome_summary, aes(x = age, y = fct_relevel(rincome, "Not applicable"))) +
  geom_point()
```

Another type of reordering is useful when you are coloring the lines on
a plot. fct_reorder2(.f, .x, .y) reorders the factor .f by the .y values
associated with the largest .x values. This makes the plot easier to
read because the colors of the line at the far right of the plot will
line up with the legend.

``` r
by_age <- gss_cat |>
  filter(!is.na(age)) |>
  count(age, marital) |>
  group_by(age) |>
  mutate(
    prop = n / sum(n)
  )

ggplot(by_age, aes(x = age, y = prop, color = marital)) +
  geom_line(linewidth = 1) +
  scale_color_brewer(palette = "Set1")

ggplot(by_age, aes(x = age, y = prop, color = fct_reorder2(marital, age, prop))) +
  geom_line(linewidth = 1) +
  scale_color_brewer(palette = "Set1") +
  labs(color = "marital")
```

Finally, for bar plots, you can use fct_infreq() to order levels in
decreasing frequency: this is the simplest type of reordering because it
doesn’t need any extra variables. Combine it with fct_rev() if you want
them in increasing frequency so that in the bar plot largest values are
on the right, not the left.

``` r
gss_cat |>
  mutate(marital = marital |> fct_infreq() |> fct_rev()) |>
  ggplot(aes(x = marital)) +
  geom_bar()
```

![](Chapter_16_Instructions_files/figure-commonmark/unnamed-chunk-1-1.png)

------------------------------------------------------------------------

## Exercises pt 2 of 3

1.  There are some suspiciously high numbers in tvhours. Is the mean a
    good summary?

2.  For each factor in gss_cat identify whether the order of the levels
    is arbitrary or principled.

3.  Why did moving “Not applicable” to the front of the levels move it
    to the bottom of the plot?

------------------------------------------------------------------------

## Modifying Factor Levels

Changing the values in the levels allows you to clarify labels for
publication, and collapse levels for high-level displays. The most
general and powerful tool is fct_recode(). It allows you to recode, or
change, the value of each level. For example, take the partyid variable
from the gss_cat data frame:

``` r
gss_cat |> count(partyid)
```

The levels are terse and inconsistent. Let’s tweak them to be longer and
use a parallel construction. Like most rename and recoding functions in
the tidyverse, the new values go on the left and the old values go on
the right:

``` r
gss_cat |>
  mutate(
    partyid = fct_recode(partyid,
      "Republican, strong"    = "Strong republican",
      "Republican, weak"      = "Not str republican",
      "Independent, near rep" = "Ind,near rep",
      "Independent, near dem" = "Ind,near dem",
      "Democrat, weak"        = "Not str democrat",
      "Democrat, strong"      = "Strong democrat"
    )
  ) |>
  count(partyid)
```

To combine groups, you can assign multiple old levels to the same new
level:

``` r
gss_cat |>
  mutate(
    partyid = fct_recode(partyid,
      "Republican, strong"    = "Strong republican",
      "Republican, weak"      = "Not str republican",
      "Independent, near rep" = "Ind,near rep",
      "Independent, near dem" = "Ind,near dem",
      "Democrat, weak"        = "Not str democrat",
      "Democrat, strong"      = "Strong democrat",
      "Other"                 = "No answer",
      "Other"                 = "Don't know",
      "Other"                 = "Other party"
    )
  )
```

Use this technique with care: if you group together categories that are
truly different you will end up with misleading results.

If you want to collapse a lot of levels, fct_collapse() is a useful
variant of fct_recode(). For each new variable, you can provide a vector
of old levels:

``` r
gss_cat |>
  mutate(
    partyid = fct_collapse(partyid,
      "other" = c("No answer", "Don't know", "Other party"),
      "rep" = c("Strong republican", "Not str republican"),
      "ind" = c("Ind,near rep", "Independent", "Ind,near dem"),
      "dem" = c("Not str democrat", "Strong democrat")
    )
  ) |>
  count(partyid)
```

Sometimes you just want to lump together the small groups to make a plot
or table simpler. That’s the job of the fct_lump\_\*() family of
functions. fct_lump_lowfreq() is a simple starting point that
progressively lumps the smallest groups categories into “Other”, always
keeping “Other” as the smallest category.

``` r
gss_cat |>
  mutate(relig = fct_lump_lowfreq(relig)) |>
  count(relig)
```

In this case it’s not very helpful: it is true that the majority of
Americans in this survey are Protestant, but we’d probably like to see
some more details! Instead, we can use the fct_lump_n() to specify that
we want exactly 10 groups:

``` r
gss_cat |>
  mutate(relig = fct_lump_n(relig, n = 10)) |>
  count(relig, sort = TRUE)
```

------------------------------------------------------------------------

## Exercises pt 3 of 3

1.  How have the proportions of people identifying as Democrat,
    Republican, and Independent changed over time?

2.  How could you collapse rincome into a small set of categories?

3.  Notice there are 9 groups (excluding other) in the fct_lump example
    above. Why not 10? (Hint: type ?fct_lump, and find the default for
    the argument other_level is “Other”.)

------------------------------------------------------------------------

## Ordered Factors

Created with the ordered() function, **ordered factors** imply a strict
ordering between levels, but don’t specify anything about the magnitude
of the differences between the levels. You use ordered factors when you
know there the levels are ranked, but there’s no precise numerical
ranking.

You can identify an ordered factor when its printed because it uses \<
symbols between the factor levels:

``` r
ordered(c("a", "b", "c"))
```

**NEXT UP:** [Chapter
17](https://github.com/UCSC-Treehouse/Essential-skills-for-Treehouse-computational-research/blob/main/Chapter-Instructions/Chapter_17_Instructions.md)
