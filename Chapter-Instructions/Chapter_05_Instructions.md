# Chapter 5, Data Tidying


In this chapter, you will learn a consistent way to organize your data
in R using a system called **tidy data**. Getting your data into this
format requires some work up front, but that work pays off in the long
term.

In this chapter, you’ll first learn the definition of tidy data and see
it applied to a simple toy dataset. Then we’ll dive into the primary
tool you’ll use for tidying data: pivoting. Pivoting allows you to
change the form of your data without changing any of the values.

------------------------------------------------------------------------

## Prerequisites

In this chapter, we’ll focus on tidyr, a package that provides a bunch
of tools to help tidy up your messy datasets. tidyr is a member of the
core tidyverse.

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

## Tidy Data

You can represent the same underlying data in multiple ways. The example
below shows the same data organized in three different ways. Each
dataset shows the same values of four variables: country, year,
population, and number of documented cases of TB (tuberculosis), but
each dataset organizes the values in a different way.

``` r
table1

table2

table3
```

**table1** will be much easier to work with inside the tidyverse because
it’s tidy.

There are three interrelated rules that make a dataset tidy:

1.  Each variable is a column; each column is a variable.
2.  Each observation is a row; each row is an observation.
3.  Each value is a cell; each cell is a single value.

<img src="Images/example_tidy_dataset.png" 
     alt="The following three rules make a dataset tidy: variables are columns, observations are rows, and values are cells."
     style="width: 55%;">

dplyr, ggplot2, and all the other packages in the tidyverse are designed
to work with tidy data. Here are a few small examples showing how you
might work with table1.

``` r
# Compute rate per 10,000
table1 |>
  mutate(rate = cases / population * 10000)

# Compute total cases per year
table1 |> 
  group_by(year) |> 
  summarize(total_cases = sum(cases))

# Visualize changes over time
ggplot(table1, aes(x = year, y = cases)) +
  geom_line(aes(group = country), color = "grey50") +
  geom_point(aes(color = country, shape = country)) +
  scale_x_continuous(breaks = c(1999, 2000)) # x-axis breaks at 1999 and 2000
```

------------------------------------------------------------------------

## Exercises pt 1 of 1

1.  For each of the sample tables, describe what each observation and
    each column represents.

2.  Sketch out the process you’d use to calculate the rate for table2
    and table3. You will need to perform four operations:

<!-- -->

1.  Extract the number of TB cases per country per year.
2.  Extract the matching population per country per year.
3.  Divide cases by population, and multiply by 10000.
4.  Store back in the appropriate place. *You haven’t yet learned all
    the functions you’d need to actually perform these operations, but
    you should still be able to think through the transformations you’d
    need.*

------------------------------------------------------------------------

## Lengthening Data

Most real analyses will require at least a little tidying. You’ll begin
by figuring out what the underlying variables and observations are.
Sometimes this is easy; other times you’ll need to consult with the
people who originally generated the data. Next, you’ll **pivot** your
data into a tidy form, with variables in the columns and observations in
the rows.

tidyr provides two functions for pivoting data: pivot_longer() and
pivot_wider(). We’ll first start with pivot_longer() because it’s the
most common case.

------------------------------------------------------------------------

## Data in Column Names

The billboard dataset records the billboard rank of songs in the year
2000:

``` r
billboard
```

In this dataset, each observation is a song. The first three columns
(artist, track and date.entered) are variables that describe the song.
Then we have 76 columns (wk1-wk76) that describe the rank of the song in
each week. Here, the column names are one variable (the week) and the
cell values are another (the rank).

To tidy this data, we’ll use pivot_longer():

``` r
billboard |> 
  pivot_longer(
    cols = starts_with("wk"), 
    names_to = "week", 
    values_to = "rank"
  )
```

After the data, there are three key arguments:

-cols specifies which columns need to be pivoted, i.e. which columns
aren’t variables. This argument uses the same syntax as select() so here
we could use !c(artist, track, date.entered) or starts_with(“wk”).

-names_to names the variable stored in the column names, we named that
variable week.

-values_to names the variable stored in the cell values, we named that
variable rank.

Note that in the code “week” and “rank” are quoted because those are new
variables we’re creating, they don’t yet exist in the data when we run
the pivot_longer() call.

Now let’s turn our attention to the resulting, longer data frame. What
happens if a song is in the top 100 for less than 76 weeks? Take 2 Pac’s
“Baby Don’t Cry”, for example. The above output suggests that it was
only in the top 100 for 7 weeks, and all the remaining weeks are filled
in with missing values. These NAs don’t really represent unknown
observations; they were forced to exist by the structure of the dataset,
so we can ask pivot_longer() to get rid of them by setting
values_drop_na = TRUE:

``` r
billboard |> 
  pivot_longer(
    cols = starts_with("wk"), 
    names_to = "week", 
    values_to = "rank",
    values_drop_na = TRUE
  )
```

The number of rows is now much lower, indicating that many rows with NAs
were dropped.

This data is now tidy, but we could make future computation a bit easier
by converting values of week from character strings to numbers using
mutate() and readr::parse_number(). parse_number() is a handy function
that will extract the first number from a string, ignoring all other
text.

``` r
billboard_longer <- billboard |> 
  pivot_longer(
    cols = starts_with("wk"), 
    names_to = "week", 
    values_to = "rank",
    values_drop_na = TRUE
  ) |> 
  mutate(
    week = parse_number(week)
  )
billboard_longer
```

Now that we have all the week numbers in one variable and all the rank
values in another, we’re in a good position to visualize how song ranks
vary over time.

``` r
billboard_longer |> 
  ggplot(aes(x = week, y = rank, group = track)) + 
  geom_line(alpha = 0.25) + 
  scale_y_reverse()
```

------------------------------------------------------------------------

## How Does Pivoting Work?

Now that you’ve seen how we can use pivoting to reshape our data, let’s
take a little time to gain some intuition about what pivoting does to
the data. Let’s start with a very simple dataset to make it easier to
see what’s happening. Suppose we have three patients with ids A, B, and
C, and we take two blood pressure measurements on each patient. We’ll
create the data with tribble(), a handy function for constructing small
tibbles by hand:

``` r
df <- tribble(
  ~id,  ~bp1, ~bp2,
   "A",  100,  120,
   "B",  140,  115,
   "C",  120,  125
)
```

We want our new dataset to have three variables: id (already exists),
measurement (the column names), and value (the cell values). To achieve
this, we need to pivot df longer:

``` r
df |> 
  pivot_longer(
    cols = bp1:bp2,
    names_to = "measurement",
    values_to = "value"
  )
```

How does the reshaping work? It’s easier to see if we think about it
column by column. As shown in the image, the values in a column that was
already a variable in the original dataset (id) need to be repeated,
once for each column that is pivoted.

<img src="Images/reshaping.png" 
     alt="A reshaped dataset of patient blood pressures."
     style="width: 55%;">

------------------------------------------------------------------------

## Many Variables in Column Names

A more challenging situation occurs when you have multiple pieces of
information crammed into the column names, and you would like to store
these in separate new variables. For example, take the who2 dataset, the
source of table1 and friends that you saw above:

``` r
who2
```

This dataset, collected by the World Health Organisation, records
information about tuberculosis diagnoses. There are two columns that are
already variables and are easy to interpret: country and year. They are
followed by 56 columns like sp_m_014, ep_m_4554, and rel_m_3544. If you
stare at these columns for long enough, you’ll notice there’s a pattern.
Each column name is made up of three pieces separated by \_. The first
piece, sp/rel/ep, describes the method used for the diagnosis, the
second piece, m/f is the gender (coded as a binary variable in this
dataset), and the third piece, 014/1524/2534/3544/4554/5564/65 is the
age range (014 represents 0-14, for example).

So in this case we have six pieces of information recorded in who2: the
country and the year (already columns); the method of diagnosis, the
gender category, and the age range category (contained in the other
column names); and the count of patients in that category (cell values).
To organize these six pieces of information in six separate columns, we
use pivot_longer() with a vector of column names for names_to and
instructors for splitting the original variable names into pieces for
names_sep as well as a column name for values_to:

``` r
who2 |> 
  pivot_longer(
    cols = !(country:year),
    names_to = c("diagnosis", "gender", "age"), 
    names_sep = "_",
    values_to = "count"
  )
```

An alternative to names_sep is names_pattern, which you can use to
extract variables from more complicated naming scenarios.

------------------------------------------------------------------------

## Data and Variable Names in the Column Headers

The next step up in complexity is when the column names include a mix of
variable values and variable names. For example, take the household
dataset:

``` r
household
```

This dataset contains data about five families, with the names and dates
of birth of up to two children. The new challenge in this dataset is
that the column names contain the names of two variables (dob, name) and
the values of another (child, with values 1 or 2). To solve this problem
we again need to supply a vector to names_to but this time we use the
special “.value” sentinel; this isn’t the name of a variable but a
unique value that tells pivot_longer() to do something different. This
overrides the usual values_to argument to use the first component of the
pivoted column name as a variable name in the output.

``` r
household |> 
  pivot_longer(
    cols = !family, 
    names_to = c(".value", "child"), 
    names_sep = "_", 
    values_drop_na = TRUE
  )
```

We again use values_drop_na = TRUE, since the shape of the input forces
the creation of explicit missing variables (e.g., for families with only
one child).

------------------------------------------------------------------------

## Widening Data

pivot_wider() makes datasets wider by increasing columns and reducing
rows and helps when one observation is spread across multiple rows.

We’ll start by looking at cms_patient_experience, a dataset from the
Centers of Medicare and Medicaid services that collects data about
patient experiences:

``` r
cms_patient_experience
```

The core unit being studied is an organization, but each organization is
spread across six rows, with one row for each measurement taken in the
survey organization. We can see the complete set of values for
measure_cd and measure_title by using distinct():

``` r
cms_patient_experience |> 
  distinct(measure_cd, measure_title)
```

pivot_wider() has the opposite interface to pivot_longer(): instead of
choosing new column names, we need to provide the existing columns that
define the values (values_from) and the column name (names_from):

``` r
cms_patient_experience |> 
  pivot_wider(
    names_from = measure_cd,
    values_from = prf_rate
  )
```

The output doesn’t look quite right; we still seem to have multiple rows
for each organization. That’s because, we also need to tell
pivot_wider() which column or columns have values that uniquely identify
each row; in this case those are the variables starting with “org”:

``` r
cms_patient_experience |> 
  pivot_wider(
    id_cols = starts_with("org"),
    names_from = measure_cd,
    values_from = prf_rate
  )
```

------------------------------------------------------------------------

## How Does pivot_wider() Work?

To understand how pivot_wider() works, let’s again start with a very
simple dataset. This time we have two patients with ids A and B, we have
three blood pressure measurements on patient A and two on patient B:

``` r
df <- tribble(
  ~id, ~measurement, ~value,
  "A",        "bp1",    100,
  "B",        "bp1",    140,
  "B",        "bp2",    115, 
  "A",        "bp2",    120,
  "A",        "bp3",    105
)
```

We’ll take the values from the value column and the names from the
measurement column:

``` r
df |> 
  pivot_wider(
    names_from = measurement,
    values_from = value
  )
```

To begin the process pivot_wider() needs to first figure out what will
go in the rows and columns. The new column names will be the unique
values of measurement.

``` r
df |> 
  distinct(measurement) |> 
  pull()
```

By default, the rows in the output are determined by all the variables
that aren’t going into the new names or values. These are called the
id_cols. Here there is only one column, but in general there can be any
number.

``` r
df |> 
  select(-measurement, -value) |> 
  distinct()
```

pivot_wider() then combines these results to generate an empty data
frame:

``` r
df |> 
  select(-measurement, -value) |> 
  distinct() |> 
  mutate(x = NA, y = NA, z = NA)
```

It then fills in all the missing values using the data in the input. In
this case, not every cell in the output has a corresponding value in the
input as there’s no third blood pressure measurement for patient B, so
that cell remains missing.

You might also wonder what happens if there are multiple rows in the
input that correspond to one cell in the output. The example below has
two rows that correspond to id “A” and measurement “bp1”:

``` r
df <- tribble(
  ~id, ~measurement, ~value,
  "A",        "bp1",    100,
  "A",        "bp1",    102,
  "A",        "bp2",    120,
  "B",        "bp1",    140, 
  "B",        "bp2",    115
)
```

If we attempt to pivot this we get an output that contains list-columns.

``` r
df |>
  pivot_wider(
    names_from = measurement,
    values_from = value
  )
```

Since you don’t know how to work with this sort of data yet, you’ll want
to follow the hint in the warning to figure out where the problem is:

``` r
df |> 
  group_by(id, measurement) |> 
  summarize(n = n(), .groups = "drop") |> 
  filter(n > 1)
```

What’s gone wrong? Repair the underlying damage or use your grouping and
summarizing skills to ensure that each combination of row and column
values only has a single row.

------------------------------------------------------------------------

**NEXT UP:** [Chapter
6](https://github.com/UCSC-Treehouse/Essential-skills-for-Treehouse-computational-research/blob/main/Chapter-Instructions/Chapter_06_Instructions.md)
