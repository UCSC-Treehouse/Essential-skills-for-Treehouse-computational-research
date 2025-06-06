# Chapter 4, Code Style


A consistent coding style makes it easier for others (including
future-you!) to read your work and is particularly important if you need
to get help from someone else. This chapter will introduce the most
important points of the tidyverse style guide, which is used throughout
our Essential Skills to Computational Research.

------------------------------------------------------------------------

## Prerequisites

You can restyle existing code with packages like **styler**. Install
this package and then find it in RStudio’s **command palette**. The
command palette lets you use any built-in RStudio command and many
addins provided by packages. Open the palette by pressing Cmd/Ctrl +
Shift + P, then type “styler” to see all the shortcuts offered.

We will again be using the tidyverse and nycflights13 packages.

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
library(nycflights13)
```

------------------------------------------------------------------------

## Names

Remember that variable names (those created by \<- and those created by
mutate()) should use only lowercase letters, numbers, and “\_”. Use “\_”
to separate words within a name. As a general rule of thumb, it’s better
to prefer long, descriptive names that are easy to understand rather
than concise names that are fast to type.

If you have a bunch of names for related things, do your best to be
consistent. It’s easy for inconsistencies to arise when you forget a
previous convention, so don’t feel bad if you have to go back and rename
things. In general, if you have a bunch of variables that are a
variation on a theme, you’re better off giving them a common prefix
rather than a common suffix because autocomplete works best on the start
of a variable.

------------------------------------------------------------------------

## Spaces

Put spaces on either side of mathematical operators apart from ^
(i.e. +, -, ==, \<, …), and around the assignment operator (\<-).

Strive for… z \<- (a + b)^2 / d

Avoid… z\<-( a + b ) ^ 2/d

Don’t put spaces inside or outside parentheses for regular function
calls. Always put a space after a comma, just like in standard English.

It’s OK to add extra spaces if it improves alignment. For example, if
you’re creating multiple variables in mutate(), you might want to add
spaces so that all the = line up. This makes it easier to skim the code.

``` r
flights |> 
    mutate(
      speed      = distance / air_time,
      dep_hour   = dep_time %/% 100,
      dep_minute = dep_time %%  100
    )
```

------------------------------------------------------------------------

## Pipes

\|\> should always have a space before it and should typically be the
last thing on a line.

``` r
#Strive for...
flights |>  
  filter(!is.na(arr_delay), !is.na(tailnum)) |> 
  count(dest)

#Avoid...
flights|>filter(!is.na(arr_delay), !is.na(tailnum))|>count(dest)
```

After the first step of the pipeline, indent each line by two spaces.
RStudio will automatically put the spaces in for you after a line break
following a \|\> . If you’re putting each argument on its own line,
indent by an extra two spaces. Make sure ) is on its own line, and
un-indented to match the horizontal position of the function name.

``` r
#Strive for...
flights |>  
  group_by(tailnum) |> 
  summarize(
    delay = mean(arr_delay, na.rm = TRUE),
    n = n()
  )
  
#Avoid...
flights|>
  group_by(tailnum) |> 
  summarize(
              delay = mean(arr_delay, na.rm = TRUE), 
              n = n()
            )
              
#Avoid...
flights|>
  group_by(tailnum) |> 
  summarize(
  delay = mean(arr_delay, na.rm = TRUE), 
  n = n()
  )
```

Finally, be wary of writing very long pipes, say longer than 10-15
lines. Try to break them up into smaller sub-tasks, giving each task an
informative name.

------------------------------------------------------------------------

## ggplot2

The same basic rules that apply to the pipe also apply to ggplot2; just
treat + the same way as \|\>.

``` r
flights |> 
    group_by(month) |> 
    summarize(
      delay = mean(arr_delay, na.rm = TRUE)
    ) |> 
    ggplot(aes(x = month, y = delay)) +
    geom_point() + 
    geom_line()
```

Watch for the transition from \|\> to +. We wish this transition wasn’t
necessary, but unfortunately, ggplot2 was written before the pipe was
discovered.

------------------------------------------------------------------------

## Sectioning Comments

As your scripts get longer, you can use sectioning comments to break up
your file into manageable pieces:

``` r
# Load data --------------------------------------

# Plot data --------------------------------------
```

RStudio provides a keyboard shortcut to create these headers (Cmd/Ctrl +
Shift + R), and will display them in the code navigation drop-down at
the bottom-left of the editor.

------------------------------------------------------------------------

## Exercises pt 1 of 1

1.  Restyle the following pipelines following the guidelines above.

``` r
flights|>filter(dest=="IAH")|>group_by(year,month,day)|>summarize(n=n(),
delay=mean(arr_delay,na.rm=TRUE))|>filter(n>10)

flights|>filter(carrier=="UA",dest%in%c("IAH","HOU"),sched_dep_time>
0900,sched_arr_time<2000)|>group_by(flight)|>summarize(delay=mean(
arr_delay,na.rm=TRUE),cancelled=sum(is.na(arr_delay)),n=n())|>filter(n>10)
```

------------------------------------------------------------------------

**NEXT UP:** [Chapter
5](https://github.com/UCSC-Treehouse/Essential-skills-for-Treehouse-computational-research/blob/main/Chapter-Instructions/Chapter_05_Instructions.md)
