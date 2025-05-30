# Chapter_00_Instructions


In this chapter, you will set up:

- A GitHub account and repository
- An RStudio project with Quarto and `renv`

------------------------------------------------------------------------

## Prerequisites

1.  **Install Required Software**

- [Github Desktop](https://github.com/apps/desktop) (use UCSC email)
- [Git](https://git-scm.com/downloads)
- [R](https://www.r-project.org)
- [RStudio](https://posit.co/download/rstudio-desktop/)
- Quarto and renv (*instructions below*)

2.  **Organization Access**

- Ask your mentor, Holly, or Ellen to add you to the ‘UCSC-Treehouse’
  GitHub organization

------------------------------------------------------------------------

## Setup Instructions

### Step 1: Create Project Folder

1.  Create a main directory on your desktop named either

- ‘UCSC-Treehouse’ or
- ‘UCSC-Vaske-Lab’

### Step 2: Create a GitHub Repository

1.  Open GitHub Desktop
2.  On the top left corner, click **“Add”** –\> **“Create New
    Repository”**

![Create New Repository in Github
Desktop](Images/add_new_repository.png){#fig-repository width=55%
fig-alt=“Image showing top left portion of GitHub Desktop screen,
navigating to”Add” and “Create New Repository…”” fig-align=“center”
.border}

3.  Configure with:

- Name: ‘YourName-Essential-Skills-in-Computational-Research’ (e.g.,
  ‘Kaylee-Essential-Skills-in-Computational-Research’)
- Local path: You new lab folder (e.g., ’~/Desktop/UCSC-Treehouse)

![Name New Repository in Github
Desktop](Images/name_your_new_repository.png){#fig-repository width=35%
fig-alt=“Image showing top left portion of GitHub Desktop screen,
navigating to”Add” and “Create New Repository…”” fig-align=“center”
.border}

### Step 3: Create Quarto Project with renv

1.  In RStudio:

- File –\> New Project –\> Existing Directory –\>
  YourName-Essential-Skills-in-Computational-Research

![Create New Project in Github
Desktop](Images/create_new_project.png){#fig-repository width=55%
fig-alt=“Image showing file button to create”New Project””
fig-align=“center” .border}

![Create New Project in Existing
Directory](Images/project_in_existing_directory.png){#fig-repository
width=55% fig-alt=“Image showing button on RStudio to”Create New
Project” within “Existing Directory”” fig-align=“center” .border}

2.  Create your first Quarto notebook:

- File –\> New File –\> Quarto Document
  <img src="Images/create_new_quarto_file.png" id="fig-file"
  class="border" style="width:45.0%"
  data-fig-alt="Image showing how to create a new quarto file in RStudio"
  data-fig-align="center" alt="Create New Quarto File in Project" />
- Title and save as ‘Chapter_01.qmd’

<img src="Images/name_new_quarto_file.png" class="border"
style="width:45.0%"
data-fig-alt="Image showing how to name and save a new quarto file in RStudio"
data-fig-align="center" alt="Name New Quarto File" />

3.  Enable Reproducibility:

- In RStudio Console run:

``` r
renv::init()
```

- Select “Yes” when prompted to restart R session
- Install required packages (e.g., ‘tidyverse’):

``` r
install.packages("tidyverse")
```

    The following package(s) will be installed:
    - tidyverse [2.0.0]
    These packages will be installed into "~/Desktop/Vaske Lab:Treehouse/Essential-skills-for-Treehouse-computational-research/Chapter-Instructions/renv/library/macos/R-4.4/aarch64-apple-darwin20".

    # Installing packages --------------------------------------------------------
    - Installing tidyverse ...                      OK [linked from cache]
    Successfully installed 1 package in 3.9 milliseconds.

- Take snapshot:

``` r
renv::snapshot()
```

    - The lockfile is already up to date.

### Step 4: General Workflow

1.  In GitHub Desktop:

- Commit changes to main with message “Initialize project with renv”

<img src="Images/commit_changes_to_git.png" class="border"
data-fig-alt="Image showing how to commit changes to Git on GitHub Desktop"
alt="Commit changes to Git" />

*note: your \# of files will be different than mine*

- Create new branch: ‘chapter-01’ (Branch –\> New Branch)

<img src="Images/create_new_branch.png" class="border"
data-fig-alt="Image showing how to create a new branch on GitHub Desktop"
alt="Create a new branch on GitHub Desktop" />

2.  Push to GitHub:

- Publish repository (first time only)
- Push changes to ‘chapter-01’ branch

3.  Request Review (**once per chapter**)

- Create Pull Request (GitHub Desktop –\> Branch –\> Create Pull
  Request)
- Assign your mentor as reviewer

------------------------------------------------------------------------

## Next Steps:

- Repeat this workflow for **each** chapter

1.  Create a ‘.qmd.’ file
2.  Work on examples, take notes where relevant, complete exercises
3.  Run ‘renv::snapshot()’ if adding packages
4.  Commit changes to a new branch (‘chapter-N’)
5.  Create a pull request for mentor review
