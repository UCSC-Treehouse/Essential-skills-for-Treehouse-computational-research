# Chapter 6, Workflow: Scripts and Projects


So far, you have been using **Quarto notebooks** (.qmd files)-
computational notebooks that blend code, text, and outputs into a single
document. These are ideal for exploratory analysis and sharing results
(like a lab notebook!), but R also supports other file types:

- **Traditional scripts (.R files)**: pure code, best for reuseable
  functions and pipelines.
- **R Markdown (.Rmd)**: Quarto’s predecessor, similar to .qmd but less
  flexible.

In this chapter we’ll focus on systematically organizing your workflow.
You will set your **working directory**, name and save files
consistently, use **R Projects**, and reference **file paths**.

------------------------------------------------------------------------

## Running Code

You should typically start your script with the packages you need. That
way, if you share your code with others, they can easily see which
packages they need to install. Note, however, that you should never
include install.packages() in a script you share.

------------------------------------------------------------------------

## Saving and Naming

Three important principles for file naming are as follows:

- File names should be **machine** readable: avoid spaces, symbols, and
  special characters. Don’t rely on case sensitivity to distinguish
  files.

- File names should be **human** readable: use file names to describe
  what’s in the file.

- File names should play well with default ordering: start file names
  with numbers so that alphabetical sorting puts them in the order they
  get used.

For example, suppose you have the following files in a project folder.

| Bad Naming                      |
|---------------------------------|
| code for exploratory analysis.r |
| finalreport.qmd                 |
| FinalReport.qmd                 |
| fig 1.png                       |
| model_first_try.R               |
| run-first.r                     |
| temp.txt                        |

There are a variety of problems here: it’s hard to find which file to
run first, file names contain spaces, there are two files with the same
name but different capitalization (finalreport vs. FinalReport), and
some names don’t describe their contents (run-first and temp).

Here’s a better way of naming and organizing the same set of files:

| Good Naming               |
|---------------------------|
| 01-load-data.R            |
| 02-exploratory-analysis.R |
| 03-model-approach-1.R     |
| 04-model-approach-2.R     |
| fig-01.png                |
| fig-02.png                |
| report-2022-03-20.qmd     |
| report-2022-04-02.qmd     |
| report-draft-notes.txt    |

------------------------------------------------------------------------

## Where Does Your Analysis Live?

R has a powerful notion of the working directory. This is where R looks
for files that you ask it to load, and where it will put any files that
you ask it to save. RStudio shows your current working directory at the
top of the console next to the “R”.

We can also print this out in R code by running getwd():

``` r
getwd()
```

------------------------------------------------------------------------

## RStudio Projects

Keeping all the files associated with a given project (input data, R
scripts, analytical results, and figures) together in one directory is
such a wise and common practice that RStudio has built-in support for
this via **projects**.

One day, you will want to remake a figure or just understand where it
came from. If you rigorously save figures to files **with R code** and
never with the mouse or the clipboard, you will be able to reproduce old
work with ease!

------------------------------------------------------------------------

## Relative and Absolute Paths

Once you’re inside a project, you should only ever use **relative
paths** not **absolute paths**. What’s the difference? A relative path
is relative to the working directory, i.e. the project’s home. Relative
paths are important because they will work regardless of where the R
project folder ends up.

Absolute paths point to the same place regardless of your working
directory. They look a little different depending on your operating
system. - On Windows they start with a drive letter (e.g., C:) or two
backslashes (e.g., “\servername”) - On Mac/Linux they start with a slash
“/” (e.g., /users/hadley) You should **NEVER** use absolute paths in
your scripts, because they hinder sharing: no one else will have exactly
the same directory configuration as you.

There’s another important difference between operating systems: how you
separate the components of the path. - Mac and Linux uses slashes (e.g.,
“data/diamonds.csv”) - Windows uses backslashes (e.g., “data.csv”) R can
work with either type (no matter what platform you’re currently using),
but unfortunately, backslashes mean something special to R, and to get a
single backslash in the path, you need to type two backslashes! That
makes life frustrating, so we recommend always using the Linux/Mac style
with forward slashes.

------------------------------------------------------------------------

**NEXT UP:** [Chapter
7](https://github.com/UCSC-Treehouse/Essential-skills-for-Treehouse-computational-research/blob/main/Chapter-Instructions/Chapter_07_Instructions.md)
