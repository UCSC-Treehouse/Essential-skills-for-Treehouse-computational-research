# Chapter 26, Iteration


In this chapter, you’ll learn tools for iteration, repeatedly performing
the same action on different objects. Iteration in R generally tends to
look rather different from other programming languages because so much
of it is implicit and we get it for free. For example, if you want to
double a numeric vector x in R, you can just write 2 \* x. In most other
languages, you’d need to explicitly double each element of x using some
sort of for loop.

This book has already given you a small but powerful number of tools
that perform the same action for multiple “things”:

- facet_wrap() and facet_grid() draws a plot for each subset.
- group_by() plus summarize() computes summary statistics for each
  subset.
- unnest_wider() and unnest_longer() create new rows and columns for
  each element of a list-column.

Now it’s time to learn some more general tools, often called
**functional programming** tools because they are built around functions
that take other functions as inputs. Learning functional programming can
easily veer into the abstract, but in this chapter we’ll keep things
concrete by focusing on three common tasks: modifying multiple columns,
reading multiple files, and saving multiple objects.

------------------------------------------------------------------------

## Prerequisites

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

## Modifying Multiple Columns

Imagine you have this simple tibble and you want to count the number of
observations and compute the median of every column.

``` r
df <- tibble(
  a = rnorm(10),
  b = rnorm(10),
  c = rnorm(10),
  d = rnorm(10)
)
```

You could do it with copy-and-paste:

``` r
df |> summarize(
  n = n(),
  a = median(a),
  b = median(b),
  c = median(c),
  d = median(d),
)
```

That breaks our *rule of thumb* to never copy and paste more than twice,
and you can imagine that this will get very tedious if you have tens or
even hundreds of columns. Instead, you can use across():

``` r
df |> summarize(
  n = n(),
  across(a:d, median),
)
```

across() has three particularly important arguments, which we’ll discuss
in detail in the following sections. You’ll use the first two every time
you use across(): the first argument, .cols, specifies which columns you
want to iterate over, and the second argument, .fns, specifies what to
do with each column. You can use the .names argument when you need
additional control over the names of output columns, which is
particularly important when you use across() with mutate(). We’ll also
discuss two important variations, if_any() and if_all(), which work with
filter().

------------------------------------------------------------------------

## Selecting Columns with .cols

The first argument to across(), .cols, selects the columns to transform.
This uses the same specifications as select(), so you can use functions
like starts_with() and ends_with() to select columns based on their
name.

There are two additional selection techniques that are particularly
useful for across(): everything() and where(). everything() is
straightforward: it selects every (non-grouping) column:

``` r
df <- tibble(
  grp = sample(2, 10, replace = TRUE),
  a = rnorm(10),
  b = rnorm(10),
  c = rnorm(10),
  d = rnorm(10)
)

df |> 
  group_by(grp) |> 
  summarize(across(everything(), median))
```

Note grouping columns (grp here) are not included in across(), because
they’re automatically preserved by summarize().

where() allows you to select columns based on their type:

- where(is.numeric) selects all numeric columns.
- where(is.character) selects all string columns.
- where(is.Date) selects all date columns.
- where(is.POSIXct) selects all date-time columns.
- where(is.logical) selects all logical columns.

Just like other selectors, you can combine these with Boolean algebra.
For example, !where(is.numeric) selects all non-numeric columns, and
starts_with(“a”) & where(is.logical) selects all logical columns whose
name starts with “a”.

------------------------------------------------------------------------

## Calling a Single Function

The second argument to across() defines how each column will be
transformed. In simple cases, as above, this will be a single existing
function. This is a pretty special feature of R: we’re passing one
function (median, mean, str_flatten, …) to another function (across).
This is one of the features that makes R a functional programming
language.

It’s important to note that we’re passing this function to across(), so
across() can call it; we’re not calling it ourselves. That means the
function name should never be followed by (). If you forget, you’ll get
an error:

``` r
df |> 
  group_by(grp) |> 
  summarize(across(everything(), median()))
```

------------------------------------------------------------------------

## Calling Multiple Functions

In more complex cases, you might want to supply additional arguments or
perform multiple transformations. Let’s motivate this problem with a
simple example: what happens if we have some missing values in our data?
median() propagates those missing values, giving us a suboptimal output:

``` r
rnorm_na <- function(n, n_na, mean = 0, sd = 1) {
  sample(c(rnorm(n - n_na, mean = mean, sd = sd), rep(NA, n_na)))
}

df_miss <- tibble(
  a = rnorm_na(5, 1),
  b = rnorm_na(5, 1),
  c = rnorm_na(5, 2),
  d = rnorm(5)
)
df_miss |> 
  summarize(
    across(a:d, median),
    n = n()
  )
```

It would be nice if we could pass along na.rm = TRUE to median() to
remove these missing values. To do so, instead of calling median()
directly, we need to create a new function that calls median() with the
desired arguments:

``` r
df_miss |> 
  summarize(
    across(a:d, function(x) median(x, na.rm = TRUE)),
    n = n()
  )
```

This is a little verbose, so R comes with a handy shortcut: for this
sort of throw away, or **anonymous**, function you can replace function
with :

``` r
df_miss |> 
  summarize(
    across(a:d, \(x) median(x, na.rm = TRUE)),
    n = n()
  )
```

In either case, across() effectively expands to the following code:

``` r
df_miss |> 
  summarize(
    a = median(a, na.rm = TRUE),
    b = median(b, na.rm = TRUE),
    c = median(c, na.rm = TRUE),
    d = median(d, na.rm = TRUE),
    n = n()
  )
```

When we remove the missing values from the median(), it would be nice to
know just how many values were removed. We can find that out by
supplying two functions to across(): one to compute the median and the
other to count the missing values. You supply multiple functions by
using a named list to .fns:

``` r
df_miss |> 
  summarize(
    across(a:d, list(
      median = \(x) median(x, na.rm = TRUE),
      n_miss = \(x) sum(is.na(x))
    )),
    n = n()
  )
```

If you look carefully, you might intuit that the columns are named using
a glue specification like {.col}\_{.fn} where .col is the name of the
original column and .fn is the name of the function. That’s not a
coincidence! As you’ll learn in the next section, you can use the .names
argument to supply your own glue spec.

------------------------------------------------------------------------

## Column Names

The result of across() is named according to the specification provided
in the .names argument. We could specify our own if we wanted the name
of the function to come first:

``` r
df_miss |> 
  summarize(
    across(
      a:d,
      list(
        median = \(x) median(x, na.rm = TRUE),
        n_miss = \(x) sum(is.na(x))
      ),
      .names = "{.fn}_{.col}"
    ),
    n = n(),
  )
```

The .names argument is particularly important when you use across() with
mutate(). By default, the output of across() is given the same names as
the inputs. This means that across() inside of mutate() will replace
existing columns. For example, here we use coalesce() to replace NAs
with 0:

``` r
df_miss |> 
  mutate(
    across(a:d, \(x) coalesce(x, 0))
  )
```

If you’d like to instead create new columns, you can use the .names
argument to give the output new names:

``` r
df_miss |> 
  mutate(
    across(a:d, \(x) coalesce(x, 0), .names = "{.col}_na_zero")
  )
```

------------------------------------------------------------------------

## Filtering

across() is a great match for summarize() and mutate() but it’s more
awkward to use with filter(), because you usually combine multiple
conditions with either \| or &. It’s clear that across() can help to
create multiple logical columns, but then what? So dplyr provides two
variants of across() called if_any() and if_all():

``` r
# same as df_miss |> filter(is.na(a) | is.na(b) | is.na(c) | is.na(d))
df_miss |> filter(if_any(a:d, is.na))

# same as df_miss |> filter(is.na(a) & is.na(b) & is.na(c) & is.na(d))
df_miss |> filter(if_all(a:d, is.na))
```

------------------------------------------------------------------------

## across() in Functions

across() is particularly useful to program with because it allows you to
operate on multiple columns. For example, this little helper which wraps
a bunch of lubridate functions to expand all date columns into year,
month, and day columns:

``` r
expand_dates <- function(df) {
  df |> 
    mutate(
      across(where(is.Date), list(year = year, month = month, day = mday))
    )
}

df_date <- tibble(
  name = c("Amy", "Bob"),
  date = ymd(c("2009-08-03", "2010-01-16"))
)

df_date |> 
  expand_dates()
```

across() also makes it easy to supply multiple columns in a single
argument because the first argument uses tidy-select; you just need to
remember to embrace that argument. For example, this function will
compute the means of numeric columns by default. But by supplying the
second argument you can choose to summarize just selected columns:

``` r
summarize_means <- function(df, summary_vars = where(is.numeric)) {
  df |> 
    summarize(
      across({{ summary_vars }}, \(x) mean(x, na.rm = TRUE)),
      n = n(),
      .groups = "drop"
    )
}
diamonds |> 
  group_by(cut) |> 
  summarize_means()

diamonds |> 
  group_by(cut) |> 
  summarize_means(c(carat, x:z))
```

------------------------------------------------------------------------

## Compare with pivot_longer()

Before we go on, it’s worth pointing out an interesting connection
between across() and pivot_longer(). In many cases, you perform the same
calculations by first pivoting the data and then performing the
operations by group rather than by column. For example, take this
multi-function summary:

``` r
df |> 
  summarize(across(a:d, list(median = median, mean = mean)))
```

We could compute the same values by pivoting longer and then
summarizing:

``` r
long <- df |> 
  pivot_longer(a:d) |> 
  group_by(name) |> 
  summarize(
    median = median(value),
    mean = mean(value)
  )
long
```

And if you wanted the same structure as across() you could pivot again:

``` r
long |> 
  pivot_wider(
    names_from = name,
    values_from = c(median, mean),
    names_vary = "slowest",
    names_glue = "{name}_{.value}"
  )
```

This is a useful technique to know about because sometimes you’ll hit a
problem that’s not currently possible to solve with across(): when you
have groups of columns that you want to compute with simultaneously. For
example, imagine that our data frame contains both values and weights
and we want to compute a weighted mean:

``` r
df_paired <- tibble(
  a_val = rnorm(10),
  a_wts = runif(10),
  b_val = rnorm(10),
  b_wts = runif(10),
  c_val = rnorm(10),
  c_wts = runif(10),
  d_val = rnorm(10),
  d_wts = runif(10)
)
```

There’s currently no way to do this with across(), but it’s relatively
straightforward with pivot_longer():

``` r
df_long <- df_paired |> 
  pivot_longer(
    everything(), 
    names_to = c("group", ".value"), 
    names_sep = "_"
  )
df_long

df_long |> 
  group_by(group) |> 
  summarize(mean = weighted.mean(val, wts))
```

------------------------------------------------------------------------

## Exercises pt 1 of 1

1.  Practice your across() skills by:

<!-- -->

1.  Computing the number of unique values in each column of
    palmerpenguins::penguins.
2.  Computing the mean of every column in mtcars.
3.  Grouping diamonds by cut, clarity, and color then counting the
    number of observations and computing the mean of each numeric
    column.

<!-- -->

2.  What happens if you use a list of functions in across(), but don’t
    name them? How is the output named?

3.  Adjust expand_dates() to automatically remove the date columns after
    they’ve been expanded. Do you need to embrace any arguments?

4.  Explain what each step of the pipeline in this function does. What
    special feature of where() are we taking advantage of?

``` r
show_missing <- function(df, group_vars, summary_vars = everything()) {
  df |> 
    group_by(pick({{ group_vars }})) |> 
    summarize(
      across({{ summary_vars }}, \(x) sum(is.na(x))),
      .groups = "drop"
    ) |>
    select(where(\(x) any(x > 0)))
}
nycflights13::flights |> show_missing(c(year, month, day))
```

------------------------------------------------------------------------

## Reading Multiple Files

In the previous section, you learned how to use dplyr::across() to
repeat a transformation on multiple columns. In this section, you’ll
learn how to use purrr::map() to do something to every file in a
directory. Let’s start with a little motivation: imagine you have a
directory full of excel spreadsheets you want to read. You could do it
with copy and paste:

``` r
data2019 <- readxl::read_excel("data/y2019.xlsx")
data2020 <- readxl::read_excel("data/y2020.xlsx")
data2021 <- readxl::read_excel("data/y2021.xlsx")
data2022 <- readxl::read_excel("data/y2022.xlsx")
```

And then use dplyr::bind_rows() to combine them all together:

``` r
data <- bind_rows(data2019, data2020, data2021, data2022)
```

INSTEAD …

------------------------------------------------------------------------

## Listing Files in a Directory

As the name suggests, list.files() lists the files in a directory.
You’ll almost always use three arguments:

- The first argument, path, is the directory to look in.
- pattern is a regular expression used to filter the file names. The
  most common pattern is something like \[.\]xlsx\$ or \[.\]csv\$ to
  find all files with a specified extension.
- full.names determines whether or not the directory name should be
  included in the output. You almost always want this to be TRUE.

To make our motivating example concrete, this book contains a folder
with 12 excel spreadsheets containing data from the gapminder package.
Each file contains one year’s worth of data for 142 countries. We can
list them all with the appropriate call to list.files():

``` r
paths <- list.files("data/gapminder", pattern = "[.]xlsx$", full.names = TRUE)
paths
```

------------------------------------------------------------------------

## Lists

Now that we have these 12 paths, we could call read_excel() 12 times to
get 12 data frames:

``` r
gapminder_1952 <- readxl::read_excel("data/gapminder/1952.xlsx")
gapminder_1957 <- readxl::read_excel("data/gapminder/1957.xlsx")
gapminder_1962 <- readxl::read_excel("data/gapminder/1962.xlsx")
 ...,
gapminder_2007 <- readxl::read_excel("data/gapminder/2007.xlsx")
```

But putting each sheet into its own variable is going to make it hard to
work with them a few steps down the road. Instead, they’ll be easier to
work with if we put them into a single object. A list is the perfect
tool for this job:

``` r
files <- list(
  readxl::read_excel("data/gapminder/1952.xlsx"),
  readxl::read_excel("data/gapminder/1957.xlsx"),
  readxl::read_excel("data/gapminder/1962.xlsx"),
  ...,
  readxl::read_excel("data/gapminder/2007.xlsx")
)
```

Now that you have these data frames in a list, how do you get one out?
You can use files\[\[i\]\] to extract the $i^{\text{th}}$ element:

``` r
files[[3]]
```

------------------------------------------------------------------------

## purrr::map() and list_rbind()

The code to collect those data frames in a list “by hand” is basically
just as tedious to type as code that reads the files one-by-one.
Happily, we can use purrr::map() to make even better use of our paths
vector. map() is similar toacross(), but instead of doing something to
each column in a data frame, it does something to each element of a
vector.map(x, f) is shorthand for:

``` r
list(
  f(x[[1]]),
  f(x[[2]]),
  ...,
  f(x[[n]])
)
```

So we can use map() to get a list of 12 data frames:

``` r
files <- map(paths, readxl::read_excel)
length(files)

files[[1]]
```

Now we can use purrr::list_rbind() to combine that list of data frames
into a single data frame:

``` r
list_rbind(files)
```

Or we could do both steps at once in a pipeline:

``` r
paths |> 
  map(readxl::read_excel) |> 
  list_rbind()
```

What if we want to pass in extra arguments to read_excel()? We use the
same technique that we used with across(). For example, it’s often
useful to peek at the first few rows of the data with n_max = 1:

``` r
paths |> 
  map(\(path) readxl::read_excel(path, n_max = 1)) |> 
  list_rbind()
```

This makes it clear that something is missing: there’s no year column
because that value is recorded in the path, not in the individual files.
We’ll tackle that problem next.

------------------------------------------------------------------------

## Data in the Path

Sometimes the name of the file is data itself. In this example, the file
name contains the year, which is not otherwise recorded in the
individual files. To get that column into the final data frame, we need
to do two things:

First, we name the vector of paths. The easiest way to do this is with
the set_names() function, which can take a function. Here we use
basename() to extract just the file name from the full path:

``` r
paths |> set_names(basename) 
```

Those names are automatically carried along by all the map functions, so
the list of data frames will have those same names:

``` r
files <- paths |> 
  set_names(basename) |> 
  map(readxl::read_excel)
```

That makes this call to map() shorthand for:

``` r
files <- list(
  "1952.xlsx" = readxl::read_excel("data/gapminder/1952.xlsx"),
  "1957.xlsx" = readxl::read_excel("data/gapminder/1957.xlsx"),
  "1962.xlsx" = readxl::read_excel("data/gapminder/1962.xlsx"),
  ...,
  "2007.xlsx" = readxl::read_excel("data/gapminder/2007.xlsx")
)
```

You can also use \[\[ to extract elements by name:

``` r
files[["1962.xlsx"]]
```

Then we use the names_to argument to list_rbind() to tell it to save the
names into a new column called year then use readr::parse_number() to
extract the number from the string.

``` r
paths |> 
  set_names(basename) |> 
  map(readxl::read_excel) |> 
  list_rbind(names_to = "year") |> 
  mutate(year = parse_number(year))
```

In more complicated cases, there might be other variables stored in the
directory name, or maybe the file name contains multiple bits of data.
In that case, use set_names() (without any arguments) to record the full
path, and then use tidyr::separate_wider_delim() and friends to turn
them into useful columns.

``` r
paths |> 
  set_names() |> 
  map(readxl::read_excel) |> 
  list_rbind(names_to = "year") |> 
  separate_wider_delim(year, delim = "/", names = c(NA, "dir", "file")) |> 
  separate_wider_delim(file, delim = ".", names = c("file", "ext"))
```

------------------------------------------------------------------------

## Save Your Work

Now that you’ve done all this hard work to get to a nice tidy data
frame, it’s a great time to save your work:

``` r
gapminder <- paths |> 
  set_names(basename) |> 
  map(readxl::read_excel) |> 
  list_rbind(names_to = "year") |> 
  mutate(year = parse_number(year))

write_csv(gapminder, "gapminder.csv")
```

If you’re working in a project, we suggest calling the file that does
this sort of data prep work something like 0-cleanup.R. The 0 in the
file name suggests that this should be run before anything else.

If your input data files change over time, you might consider learning a
tool like targets to set up your data cleaning code to automatically
re-run whenever one of the input files is modified.

------------------------------------------------------------------------

## Many Simple Iterations

Here we’ve just loaded the data directly from disk, and were lucky
enough to get a tidy dataset. In most cases, you’ll need to do some
additional tidying, and you have two basic options: you can do one round
of iteration with a complex function, or do multiple rounds of iteration
with simple functions. In our experience most folks reach first for one
complex iteration, but you’re often better by doing multiple simple
iterations.

For example, imagine that you want to read in a bunch of files, filter
out missing values, pivot, and then combine. One way to approach the
problem is to write a function that takes a file and does all those
steps then call map() once:

``` r
process_file <- function(path) {
  df <- read_csv(path)
  
  df |> 
    filter(!is.na(id)) |> 
    mutate(id = tolower(id)) |> 
    pivot_longer(jan:dec, names_to = "month")
}

paths |> 
  map(process_file) |> 
  list_rbind()
```

Alternatively, you could perform each step of process_file() to every
file:

``` r
paths |> 
  map(read_csv) |> 
  map(\(df) df |> filter(!is.na(id))) |> 
  map(\(df) df |> mutate(id = tolower(id))) |> 
  map(\(df) df |> pivot_longer(jan:dec, names_to = "month")) |> 
  list_rbind()
```

We recommend this approach because it stops you getting fixated on
getting the first file right before moving on to the rest. By
considering all of the data when doing tidying and cleaning, you’re more
likely to think holistically and end up with a higher quality result.

In this particular example, there’s another optimization you could make,
by binding all the data frames together earlier. Then you can rely on
regular dplyr behavior:

``` r
paths |> 
  map(read_csv) |> 
  list_rbind() |> 
  filter(!is.na(id)) |> 
  mutate(id = tolower(id)) |> 
  pivot_longer(jan:dec, names_to = "month")
```

------------------------------------------------------------------------

## Heterogenous Data

Unfortunately, sometimes it’s not possible to go from map() straight to
list_rbind() because the data frames are so heterogeneous that
list_rbind() either fails or yields a data frame that’s not very useful.
In that case, it’s still useful to start by loading all of the files:

``` r
files <- paths |> 
  map(readxl::read_excel) 
```

Then a very useful strategy is to capture the structure of the data
frames so that you can explore it using your data science skills. One
way to do so is with this handy df_types function that returns a tibble
with one row for each column:

``` r
df_types <- function(df) {
  tibble(
    col_name = names(df), 
    col_type = map_chr(df, vctrs::vec_ptype_full),
    n_miss = map_int(df, \(x) sum(is.na(x)))
  )
}

df_types(gapminder)
```

You can then apply this function to all of the files, and maybe do some
pivoting to make it easier to see where the differences are. For
example, this makes it easy to verify that the gapminder spreadsheets
that we’ve been working with are all quite homogeneous:

``` r
files |> 
  map(df_types) |> 
  list_rbind(names_to = "file_name") |> 
  select(-n_miss) |> 
  pivot_wider(names_from = col_name, values_from = col_type)
```

If the files have heterogeneous formats, you might need to do more
processing before you can successfully merge them. Unfortunately, we’re
now going to leave you to figure that out on your own, but you might
want to read about map_if() and map_at(). map_if() allows you to
selectively modify elements of a list based on their values; map_at()
allows you to selectively modify elements based on their names.

------------------------------------------------------------------------

## Handling Failures

Sometimes the structure of your data might be sufficiently wild that you
can’t even read all the files with a single command. And then you’ll
encounter one of the downsides of map(): it succeeds or fails as a
whole. map() will either successfully read all of the files in a
directory or fail with an error, reading zero files. This is annoying:
why does one failure prevent you from accessing all the other successes?

Luckily, purrr comes with a helper to tackle this problem: possibly().
possibly() is what’s known as a function operator: it takes a function
and returns a function with modified behavior. In particular, possibly()
changes a function from erroring to returning a value that you specify:

``` r
files <- paths |> 
  map(possibly(\(path) readxl::read_excel(path), NULL))

data <- files |> list_rbind()
```

This works particularly well here because list_rbind(), like many
tidyverse functions, automatically ignores NULLs.

Now you have all the data that can be read easily, and it’s time to
tackle the hard part of figuring out why some files failed to load and
what to do about it. Start by getting the paths that failed:

``` r
failed <- map_vec(files, is.null)
paths[failed]
```

Then call the import function again for each failure and figure out what
went wrong.

------------------------------------------------------------------------

## Writing to a Database

Sometimes when working with many files at once, it’s not possible to fit
all your data into memory at once, and you can’t do map(files,
read_csv). One approach to deal with this problem is to load your data
into a database so you can access just the bits you need with dbplyr.

If you’re lucky, the database package you’re using will provide a handy
function that takes a vector of paths and loads them all into the
database. This is the case with duckdb’s duckdb_read_csv():

``` r
con <- DBI::dbConnect(duckdb::duckdb())
duckdb::duckdb_read_csv(con, "gapminder", paths)
```

This would work well here, but we don’t have csv files, instead we have
excel spreadsheets. So we’re going to have to do it “by hand”. Learning
to do it by hand will also help you when you have a bunch of csvs and
the database that you’re working with doesn’t have one function that
will load them all in.

We need to start by creating a table that we will fill in with data. The
easiest way to do this is by creating a template, a dummy data frame
that contains all the columns we want, but only a sampling of the data.
For the gapminder data, we can make that template by reading a single
file and adding the year to it:

``` r
template <- readxl::read_excel(paths[[1]])
template$year <- 1952
template
```

Now we can connect to the database, and use DBI::dbCreateTable() to turn
our template into a database table:

``` r
con <- DBI::dbConnect(duckdb::duckdb())
DBI::dbCreateTable(con, "gapminder", template)
```

dbCreateTable() doesn’t use the data in template, just the variable
names and types. So if we inspect the gapminder table now you’ll see
that it’s empty but it has the variables we need with the types we
expect:

``` r
con |> tbl("gapminder")
```

Next, we need a function that takes a single file path, reads it into R,
and adds the result to the gapminder table. We can do that by combining
read_excel() with DBI::dbAppendTable():

``` r
append_file <- function(path) {
  df <- readxl::read_excel(path)
  df$year <- parse_number(basename(path))
  
  DBI::dbAppendTable(con, "gapminder", df)
}
```

Now we need to call append_file() once for each element of paths. That’s
certainly possible with map():

``` r
paths |> map(append_file)
```

Now we can see if we have all the data in our table:

``` r
con |> 
  tbl("gapminder") |> 
  count(year)
```

------------------------------------------------------------------------

## Writing csv Files

The same basic principle applies if we want to write multiple csv files,
one for each group. Let’s imagine that we want to take the
ggplot2::diamonds data and save one csv file for each clarity. First we
need to make those individual datasets. There are many ways you could do
that, but there’s one way we particularly like: group_nest().

``` r
by_clarity <- diamonds |> 
  group_nest(clarity)

by_clarity
```

This gives us a new tibble with eight rows and two columns. clarity is
our grouping variable and data is a list-column containing one tibble
for each unique value of clarity:

``` r
by_clarity$data[[1]]
```

While we’re here, let’s create a column that gives the name of output
file, using mutate() and str_glue():

``` r
by_clarity <- by_clarity |> 
  mutate(path = str_glue("diamonds-{clarity}.csv"))

by_clarity
```

So if we were going to save these data frames by hand, we might write
something like:

``` r
write_csv(by_clarity$data[[1]], by_clarity$path[[1]])
write_csv(by_clarity$data[[2]], by_clarity$path[[2]])
write_csv(by_clarity$data[[3]], by_clarity$path[[3]])
...
write_csv(by_clarity$by_clarity[[8]], by_clarity$path[[8]])
```

This is a little different to our previous uses of map() because there
are two arguments that are changing, not just one. That means we need a
new function: map2(), which varies both the first and second arguments.
And because we again don’t care about the output, we want walk2() rather
than map2(). That gives us:

``` r
walk2(by_clarity$data, by_clarity$path, write_csv)
```

------------------------------------------------------------------------

## Saving Plots

We can take the same basic approach to create many plots. Let’s first
make a function that draws the plot we want:

``` r
carat_histogram <- function(df) {
  ggplot(df, aes(x = carat)) + geom_histogram(binwidth = 0.1)  
}

carat_histogram(by_clarity$data[[1]])
```

Now we can use map() to create a list of many plots and their eventual
file paths:

``` r
by_clarity <- by_clarity |> 
  mutate(
    plot = map(data, carat_histogram),
    path = str_glue("clarity-{clarity}.png")
  )
```

Then use walk2() with ggsave() to save each plot:

``` r
walk2(
  by_clarity$path,
  by_clarity$plot,
  \(path, plot) ggsave(path, plot, width = 6, height = 6)
)
```

This is shorthand for:

``` r
ggsave(by_clarity$path[[1]], by_clarity$plot[[1]], width = 6, height = 6)
ggsave(by_clarity$path[[2]], by_clarity$plot[[2]], width = 6, height = 6)
ggsave(by_clarity$path[[3]], by_clarity$plot[[3]], width = 6, height = 6)
...
ggsave(by_clarity$path[[8]], by_clarity$plot[[8]], width = 6, height = 6)
```

## CONGRATULATIONS! You have completed your Essential Skills in Computational Research training for Treehouse! Welcome to the team :)
