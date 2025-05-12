# Intro to Computational Research

#### *Onboarding Repository*

Welcome to your introductory guide to computational research!

This guide has been created to walk you through basic software downloads and installations as well as highlighted chapters of the ["R for Data Science"](https://r4ds.hadley.nz) book.

This guide was created by the [Vaske Lab](https://vaskelab.ucsc.edu) at UC Santa Cruz; all software links reflect current version as of Q2 2024).

------------------------------------------------------------------------

## Background

Read more about our research here: [Treehouse Childhood Cancer Initiative](https://treehousegenomics.ucsc.edu)

## Goals

-   Using Terminal, GitHub, RStudio, renv, and Quarto notebooks
-   Building repositories, files, and pipelines
-   Getting a hang of research computing workflows
-   Learning computational research skills in R including: data visualization, data transformation, and program iteration

------------------------------------------------------------------------

## Getting Started

0.  Prerequisites/Installations

-   Create a [GitHub account](https://docs.github.com/en/get-started/start-your-journey/creating-an-account-on-github) with your UCSC email and join UCSC-Treehouse organization
-   Install [Git](https://git-scm.com/downloads)
-   Install [R](https://www.r-project.org)
-   Install [RStudio](https://posit.co/download/rstudio-desktop/)
-   Install Quarto and renv (*instructions below*)

1.  Create a directory (*instructions below*)
2.  Explore the chapters (ch. 1-19, 26) of "R for Data Science" in order, creating a Quarto notebook for each

------------------------------------------------------------------------

### Creating a Directory:

0.  Follow above prerequisites

1.  Open Terminal

-   **Windows**: Press `Win + R`, type `cmd`, hit Enter
-   **Mac**: Press `Cmd + Space`, type "Terminal", hit Enter You'll see a blinking cursor like this:

``` bash
username@computer-name ~ %
```

2.  Create and navigate to new project folder

-   First create a folder for Vaske-Lab or Treehouse by copying the following into Terminal
-   Then enter the new folder

``` bash
mkdir Vaske-Lab # make directory
cd Vaske-Lab # enter directory
```

Your terminal prompt should now show:

``` bash
username@computer-name Vaske-Lab %
```

*username and computer-name will be your local computer info*

### Install Quarto and renv

Run these commands in **RStudio's console**

``` r
# install the Quarto R package
install.packages("quarto")
# install renv R package for reproducibility
install.packages("renv")
# initialize project-specific environment (do this ONCE per project)
renv::init()
```
