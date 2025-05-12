# Intro to Computational Research

#### *Onboarding Repository*

Welcome to your introductory guide to computational research!

This guide has been created to walk you through basic software downloads and installations as well as highlighted chapters of the ["R for Data Science"](https://r4ds.hadley.nz) book.

This guide was created by the [Vaske Lab](https://vaskelab.ucsc.edu) at UC Santa Cruz; all software links reflect current version as of Q2 2024).

------------------------------------------------------------------------

## Background

Read more about our research here: [Treehouse Childhood Cancer Initiative](https://treehousegenomics.ucsc.edu)

## Goals

In order for science to be valid, it has to be reproducible. 
Luckily, this can be easier for computational biology experiments than in wet lab experiments. 

Before you can work on a Treehouse computational project, you must demonstrate that you know how to perform reproducible experiments. 
When you have completed the work described in this repo, please reach out to your mentor so you can proceed with your project. 

-   Using Terminal, GitHub, RStudio, renv, and Quarto notebooks
-   Building repositories, files, and pipelines
-   Getting a hang of research computing workflows
-   Learning computational research skills in R including: data visualization, data transformation, and program iteration

------------------------------------------------------------------------

## Getting Started

0.  Prerequisites/Installations

-   Create a [GitHub account](https://docs.github.com/en/get-started/start-your-journey/creating-an-account-on-github)
-   Ask your mentor, Holly, or Ellen to add you to the UCSC-Treehouse GitHub organization
-   Install [Git](https://git-scm.com/downloads)
-   Install [R](https://www.r-project.org)
-   Install [RStudio](https://posit.co/download/rstudio-desktop/)
-   Install Quarto and renv (*instructions below*)

1.  Create a directory with quarto notebooks (*instructions below*)
2.  Explore the chapters (ch. 1-19, 26) of "R for Data Science" in order

------------------------------------------------------------------------

### Creating a Directory and Adding Quarto Notebooks:

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
3. Install Quarto and renv
Run these commands in **RStudio's console**

``` r
# install the Quarto R package
install.packages("quarto")
# install renv R package for reproducibility
install.packages("renv")
# initialize project-specific environment (do this ONCE per project)
renv::init()
```
4. Create quarto notebooks for each textbook chapter (Ch. 1-19, 26)
Run these commands back in your **Terminal**

``` bash
# Create properly numbered quarto notebook for each of chapters 1-19, 26
for i in {01..19} 26; do
  touch Chapter_$i.qmd
done
```