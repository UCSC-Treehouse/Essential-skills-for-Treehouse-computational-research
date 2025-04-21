---
editor_options: 
  markdown: 
    wrap: 72
---

# Intro to Computational Research

#### *Onboarding Repository*

Welcome to your introductory guide to computational research!

This guide has been created to walk you through basic software downloads
and installations as well as highlighted chapters of the ["R for Data
Science"](https://r4ds.hadley.nz) book.

This guide was created by the [Vaske Lab](https://vaskelab.ucsc.edu) at
UC Santa Cruz; all software links reflect current version as of Q2
2024).

------------------------------------------------------------------------

## Background

Read more about our research here: [Treehouse Childhood Cancer
Initiative](https://treehousegenomics.ucsc.edu)

## Goals

-   Using Terminal, GitHub, RStudio, renv, and Quarto notebooks
-   Building repositories, files, and pipelines
-   Getting a hang of research computing workflows
-   Learning computational research skills in R including: data
    visualization, data transformation, and program iteration

------------------------------------------------------------------------

## Getting Started

0.  Prerequisites/Installations

-   Create a [GitHub
    account](https://docs.github.com/en/get-started/start-your-journey/creating-an-account-on-github)
    with your UCSC email and join UCSC-Treehouse organization
-   Install [Git](https://git-scm.com/downloads)
-   Install [R](https://www.r-project.org)
-   Install [RStudio](https://posit.co/download/rstudio-desktop/)
-   Install Quarto and renv (*instructions below*)

1.  Clone this repository (*instructions below*)
2.  Explore the chapters of "R for Data Science" in order of Quarto
    notebooks

------------------------------------------------------------------------

### Cloning a Repository:

To get a local (ie on *your* computer) copy of this project:

0.  Follow above prerequisites

1.  Open Terminal

-   **Windows**: Press `Win + R`, type `cmd`, hit Enter
-   **Mac**: Press `Cmd + Space`, type "Terminal", hit Enter You'll see
    a blinking cursor like this:

``` bash
username@computer-name ~ %
```

2.  Create and navigate to new project folder

-   First create a folder for Vaske-Lab or Treehouse by copying the
    following into Terminal
-   Then enter the new folder

``` bash
mkdir Vaske-Lab # make directory
cd Vaske-Lab # enter directory
```

3.  Clone this repository by copying the following into Terminal

``` bash
git clone https://github.com/UCSC-Treehouse/Intro-to-Computational-Research.git
```

You should see:

``` bash
Cloning into 'Intro-to-Computational-Research'...
remote: Enumerating objects: 100, done.
remote: Counting objects: 100% (100/100), done.
Receiving objects: 100% (100/100), 1.23 MiB | 2.45 MiB/s, done.
```

4.  Enter the new repository folder by copying the following into
    Terminal

``` bash
cd Intro-to-Computational-Research
```

Your terminal prompt should now show:

``` bash
username@computer-name Intro-to-Computational-Research %
```

*username and computer-name will be your local computer info*

5.  List all files to confirm cloning worked:

``` bash
ls
```

You should see `README.md` and other project files.

#### Troubleshooting

**If you get errors**:

1.  `git: command not found` → [Install
    Git](https://git-scm.com/downloads) first

2.  `Permission denied` → Try the HTTPS URL instead of SSH

3.  Wrong folder? Run `cd ..` to go back, then retry

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
