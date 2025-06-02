# Chapter 14, Strings


Now it’s time to dive into **strings**, learn what makes them tick, and
master some of the powerful string manipulation tools you have at your
disposal.

We’ll begin with the details of creating strings and character vectors.
You’ll then dive into creating strings from data, then the opposite:
extracting strings from data. We’ll then discuss tools that work with
individual letters. The chapter finishes with functions that work with
individual letters and a brief discussion of where your expectations
from English might steer you wrong when working with other languages.

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

``` r
library(babynames)
```

------------------------------------------------------------------------

## Creating a String

Firstly, you can create a string using either single quotes (’) or
double quotes (“).

``` r
string1 <- "This is a string"
string2 <- 'If I want to include a "quote" inside a string, I use single quotes'
```

------------------------------------------------------------------------

## Escapes

To include a literal single or double quote in a string, you can use  to
“escape” it:

``` r
double_quote <- "\"" # or '"'
single_quote <- '\'' # or "'"
```

So if you want to include a literal backslash in your string, you’ll
need to escape it: “\\”:

``` r
backslash <- "\\"
```

Beware that the printed representation of a string is not the same as
the string itself because the printed representation shows the escapes
(in other words, when you print a string, you can copy and paste the
output to recreate that string). To see the raw contents of the string,
use str_view():

``` r
x <- c(single_quote, double_quote, backslash)
x
str_view(x)
```

------------------------------------------------------------------------

## Raw Strings

Creating a string with multiple quotes or backslashes gets confusing
quickly. To illustrate the problem, let’s create a string that contains
the contents of the code block where we define the double_quote and
single_quote variables:

``` r
tricky <- "double_quote <- \"\\\"\" # or '\"'
single_quote <- '\\'' # or \"'\""
str_view(tricky)
```

That’s a lot of backslashes! (This is sometimes called *leaning
toothpick syndrome*). To eliminate the escaping, you can instead use a
raw string:

``` r
tricky <- r"(double_quote <- "\"" # or '"'
single_quote <- '\'' # or "'")"
str_view(tricky)
```

A raw string usually starts with r”( and finishes with )“. But if your
string contains )” you can instead use r”\[\]” or r”{}“, and if that’s
still not enough, you can insert any number of dashes to make the
opening and closing pairs unique, e.g., r”–()–“, r”—()—“, etc. Raw
strings are flexible enough to handle any text.

------------------------------------------------------------------------

## Other Special Characters

As well as ", ', and \\ there are a handful of other special characters
that may come in handy. The most common are , a new line, and tab.
You’ll also sometimes see strings containing Unicode escapes that start
with r This is a way of writing non-English characters that work on all
systems. You can see the complete list of other special characters in
?Quotes.

``` r
x <- c("one\ntwo", "one\ttwo", "\u00b5", "\U0001f604")
x
str_view(x)
```

------------------------------------------------------------------------

## Exercises pt 1 of 3

1.  Create strings that contain the following values:

<!-- -->

1.  He said “That’s amazing!”
2.  
3.  \\\\

<!-- -->

2.  Create the string in your R session and print it. What happens to
    the special “0a0”? How does str_view() display it? Can you do a
    little googling to figure out what this special character is?

``` r
x <- "This\u00a0is\u00a0tricky"
```

------------------------------------------------------------------------

## Creating many strings from data

Next, let’s go into the details of creating strings from other strings.
This will help you solve the common problem where you have some text you
wrote that you want to combine with strings from a data frame. For
example, you might combine “Hello” with a name variable to create a
greeting. We’ll show you how to do this with str_c() and str_glue() and
how you can use them with mutate(). That naturally raises the question
of what stringr functions you might use with summarize(), so we’ll
finish this section with a discussion of str_flatten(), which is a
summary function for strings.

------------------------------------------------------------------------

## str_c()

str_c() takes any number of vectors as arguments and returns a character
vector:

``` r
str_c("x", "y")
str_c("x", "y", "z")
str_c("Hello ", c("John", "Susan"))
```

str_c() is very similar to the base paste0(), but is designed to be used
with mutate() by obeying the usual tidyverse rules for recycling and
propagating missing values:

``` r
df <- tibble(name = c("Flora", "David", "Terra", NA))
df |> mutate(greeting = str_c("Hi ", name, "!"))
```

If you want missing values to display in another way, use coalesce() to
replace them. Depending on what you want, you might use it either inside
or outside of str_c():

``` r
df |> 
  mutate(
    greeting1 = str_c("Hi ", coalesce(name, "you"), "!"),
    greeting2 = coalesce(str_c("Hi ", name, "!"), "Hi!")
  )
```

------------------------------------------------------------------------

## str_glue()

If you are mixing many fixed and variable strings with str_c(), you’ll
notice that you type a lot of “s, making it hard to see the overall goal
of the code. An alternative approach is provided by the glue package via
str_glue(). You give it a single string that has a special feature:
anything inside {} will be evaluated like it’s outside of the quotes:

``` r
df |> mutate(greeting = str_glue("Hi {name}!"))
```

As you can see, str_glue() currently converts missing values to the
string “NA”, unfortunately making it inconsistent with str_c().

You also might wonder what happens if you need to include a regular { or
} in your string. You’re on the right track if you guess you’ll need to
escape it somehow. The trick is that glue uses a slightly different
escaping technique: instead of prefixing with special character like ,
you double up the special characters:

``` r
df |> mutate(greeting = str_glue("{{Hi {name}!}}"))
```

------------------------------------------------------------------------

## str_flatten()

str_flatten(): takes a character vector and combines each element of the
vector into a single string:

``` r
str_flatten(c("x", "y", "z"))
str_flatten(c("x", "y", "z"), ", ")
str_flatten(c("x", "y", "z"), ", ", last = ", and ")
```

This makes it work well with summarize():

``` r
df <- tribble(
  ~ name, ~ fruit,
  "Carmen", "banana",
  "Carmen", "apple",
  "Marvin", "nectarine",
  "Terence", "cantaloupe",
  "Terence", "papaya",
  "Terence", "mandarin"
)
df |>
  group_by(name) |> 
  summarize(fruits = str_flatten(fruit, ", "))
```

------------------------------------------------------------------------

## Exercises pt 2 of 3

1.  Compare and contrast the results of paste0() with str_c() for the
    following inputs:

``` r
str_c("hi ", NA)
str_c(letters[1:2], letters[1:3])
```

2.  What’s the difference between paste() and paste0()? How can you
    recreate the equivalent of paste() with str_c()?

3.  Convert the following expressions from str_c() to str_glue() or vice
    versa:

<!-- -->

1.  str_c(“The price of”, food, ” is “, price)
2.  str_glue(“I’m {age} years old and live in {country}”)
3.  str_c(“\section{”, title, “}”)

------------------------------------------------------------------------

## Extracting Data from Strings

It’s very common for multiple variables to be crammed together into a
single string. In this section, you’ll learn how to use four tidyr
functions to extract them:

- df \|\> separate_longer_delim(col, delim)
- df \|\> separate_longer_position(col, width)
- df \|\> separate_wider_delim(col, delim, names)
- df \|\> separate_wider_position(col, widths)

If you look closely, you can see there’s a common pattern here:
separate\_, then longer or wider, then \_, then by delim or position.
That’s because these four functions are composed of two simpler
primitives:

- Just like with pivot_longer() and pivot_wider(), \_longer functions
  make the input data frame longer by creating new rows and \_wider
  functions make the input data frame wider by generating new columns.
- delim splits up a string with a delimiter like “,” or ” “; position
  splits at specified widths, like c(3, 5, 2).

------------------------------------------------------------------------

## Separating into Rows

Separating a string into rows tends to be most useful when the number of
components varies from row to row. The most common case is requiring
separate_longer_delim() to split based on a delimiter:

``` r
df1 <- tibble(x = c("a,b,c", "d,e", "f"))
df1 |> 
  separate_longer_delim(x, delim = ",")
```

It’s rarer to see separate_longer_position() in the wild, but some older
datasets do use a very compact format where each character is used to
record a value:

``` r
df2 <- tibble(x = c("1211", "131", "21"))
df2 |> 
  separate_longer_position(x, width = 1)
```

------------------------------------------------------------------------

## Separating into Columns

Separating a string into columns tends to be most useful when there are
a fixed number of components in each string, and you want to spread them
into columns. They are slightly more complicated than their longer
equivalents because you need to name the columns. For example, in this
following dataset, x is made up of a code, an edition number, and a
year, separated by “.”. To use separate_wider_delim(), we supply the
delimiter and the names in two arguments:

``` r
df3 <- tibble(x = c("a10.1.2022", "b10.2.2011", "e15.1.2015"))
df3 |> 
  separate_wider_delim(
    x,
    delim = ".",
    names = c("code", "edition", "year")
  )
```

If a specific piece is not useful you can use an NA name to omit it from
the results:

``` r
df3 |> 
  separate_wider_delim(
    x,
    delim = ".",
    names = c("code", NA, "year")
  )
```

separate_wider_position() works a little differently because you
typically want to specify the width of each column. So you give it a
named integer vector, where the name gives the name of the new column,
and the value is the number of characters it occupies. You can omit
values from the output by not naming them:

``` r
df4 <- tibble(x = c("202215TX", "202122LA", "202325CA")) 
df4 |> 
  separate_wider_position(
    x,
    widths = c(year = 4, age = 2, state = 2)
  )
```

------------------------------------------------------------------------

## Diagnosing Widening Problems

separate_wider_delim() requires a fixed and known set of columns. What
happens if some of the rows don’t have the expected number of pieces?
There are two possible problems, too few or too many pieces, so
separate_wider_delim() provides two arguments to help: too_few and
too_many. Let’s first look at the too_few case with the following sample
dataset:

``` r
df <- tibble(x = c("1-1-1", "1-1-2", "1-3", "1-3-2", "1"))

df |> 
  separate_wider_delim(
    x,
    delim = "-",
    names = c("x", "y", "z")
  )
```

You’ll notice that we get an error, but the error gives us some
suggestions on how you might proceed. Let’s start by debugging the
problem:

``` r
debug <- df |> 
  separate_wider_delim(
    x,
    delim = "-",
    names = c("x", "y", "z"),
    too_few = "debug"
  )

debug
```

When you use the debug mode, you get three extra columns added to the
output: x_ok, x_pieces, and x_remainder (if you separate a variable with
a different name, you’ll get a different prefix). Here, x_ok lets you
quickly find the inputs that failed:

``` r
debug |> filter(!x_ok)
```

x_pieces tells us how many pieces were found, compared to the expected 3
(the length of names). x_remainder isn’t useful when there are too few
pieces, but we’ll see it again shortly.

Sometimes looking at this debugging information will reveal a problem
with your delimiter strategy or suggest that you need to do more
preprocessing before separating. In that case, fix the problem upstream
and make sure to remove too_few = “debug” to ensure that new problems
become errors.

In other cases, you may want to fill in the missing pieces with NAs and
move on. That’s the job of too_few = “align_start” and too_few =
“align_end” which allow you to control where the NAs should go:

``` r
df |> 
  separate_wider_delim(
    x,
    delim = "-",
    names = c("x", "y", "z"),
    too_few = "align_start"
  )
```

The same principles apply if you have too many pieces:

``` r
df <- tibble(x = c("1-1-1", "1-1-2", "1-3-5-6", "1-3-2", "1-3-5-7-9"))

df |> 
  separate_wider_delim(
    x,
    delim = "-",
    names = c("x", "y", "z")
  )
```

But now, when we debug the result, you can see the purpose of
x_remainder:

``` r
debug <- df |> 
  separate_wider_delim(
    x,
    delim = "-",
    names = c("x", "y", "z"),
    too_many = "debug"
  )

debug |> filter(!x_ok)
```

You have a slightly different set of options for handling too many
pieces: you can either silently “drop” any additional pieces or “merge”
them all into the final column:

``` r
df |> 
  separate_wider_delim(
    x,
    delim = "-",
    names = c("x", "y", "z"),
    too_many = "drop"
  )

df |> 
  separate_wider_delim(
    x,
    delim = "-",
    names = c("x", "y", "z"),
    too_many = "merge"
  )
```

------------------------------------------------------------------------

## Letters: Length

str_length() tells you the number of letters in the string:

``` r
str_length(c("a", "R for data science", NA))
```

You could use this with count() to find the distribution of lengths of
US babynames and then with filter() to look at the longest names, which
happen to have 15 letters:

``` r
babynames |>
  count(length = str_length(name), wt = n)
  
babynames |> 
  filter(str_length(name) == 15) |> 
  count(name, wt = n, sort = TRUE)
```

------------------------------------------------------------------------

## Letters: Subsetting

You can extract parts of a string using str_sub(string, start, end),
where start and end are the positions where the substring should start
and end. The start and end arguments are inclusive, so the length of the
returned string will be end - start + 1:

``` r
x <- c("Apple", "Banana", "Pear")
str_sub(x, 1, 3)
```

You can use negative values to count back from the end of the string: -1
is the last character, -2 is the second to last character, etc.

``` r
str_sub(x, -3, -1)
```

Note that str_sub() won’t fail if the string is too short: it will just
return as much as possible:

``` r
str_sub("a", 1, 5)
```

We could use str_sub() with mutate() to find the first and last letter
of each name:

``` r
babynames |> 
  mutate(
    first = str_sub(name, 1, 1),
    last = str_sub(name, -1, -1)
  )
```

------------------------------------------------------------------------

## Exercises pt 3 of 3

1.  When computing the distribution of the length of babynames, why did
    we use wt = n?

2.  Use str_length() and str_sub() to extract the middle letter from
    each baby name. What will you do if the string has an even number of
    characters?

3.  Are there any major trends in the length of babynames over time?
    What about the popularity of first and last letters?

**NEXT UP:** [Chapter
15](https://github.com/UCSC-Treehouse/Essential-skills-for-Treehouse-computational-research/blob/main/Chapter-Instructions/Chapter_15_Instructions.md)
