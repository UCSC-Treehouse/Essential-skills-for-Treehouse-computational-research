# Chapter 0


In this chapter, you will set up:

- A GitHub account and repository
- An RStudio project with Quarto and `renv`

------------------------------------------------------------------------

## Prerequisites

1.  **Install Required Software**

- [Github Desktop](https://github.com/apps/desktop)
- [R](https://www.r-project.org)
- [RStudio](https://posit.co/download/rstudio-desktop/)
- Quarto and `renv` (*instructions below*)

2.  **Organization Access**

- Ask your mentor, Holly, or Ellen to add you to the ‘UCSC-Treehouse’
  GitHub organization. **Please include your GitHub id in your
  request.**

------------------------------------------------------------------------

## Install Quarto and renv

If you have downloaded the most recent version of RStudio, Quarto will
be included (2024.04+). However, if RStudio has not included Quarto for
you, you can download it [here](https://quarto.org/docs/download/).

Next, we want to install `renv`, which captures the packages and
versions your project uses, aiding in reproducibility.

Open RStudio. Under the “Console” tab, and after the “\>”, you can go
ahead and install the `renv` package. To do this, type:
install.packages(“renv”). (**Hint**: You may notice that RStudio
suggests autofilling names of functions and packages. To complete this
autofill, you can either click the appropriate name, or, navigate to the
name using your keyboard’s up/down arrows, and press “tab”.)

<img src="Images/install_renv_in_console.png" 
     alt="Install 'renv' package with install.packages() in the R Console" 
     style="width: 55%;">

When prompted to proceed with installation, type “Y”.

------------------------------------------------------------------------

## Create a New Project with renv

In RStudio:

- File –\> New Project –\> New Directory –\> Quarto Project

<img src="Images/create_new_project.png" 
     alt="Create New Project in RStudio with 'File', 'New Project...'" 
     style="width: 55%;">

<img src="Images/project_in_existing_directory.png" 
     alt="Create New Project in RStudio with 'New Directory'" 
     style="width: 55%;">

<img src="Images/new_quarto_project.png" 
     alt="Create New Quarto Project in RStudio with 'Quarto Project'" 
     style="width: 55%;">

- When creating your new Quarto project, you want to name it in the
  following format:
  **YourName_essential_skills_in_computational_research**

- Create project in your desktop, unless you want it to be a
  subdirectory of a Vaske Lab or Treehouse folder.

In this case, create this folder and browse to select it. In the example
below, my new project will be created as a subdirectory of my lab folder
titled “Vaske_Lab_Treehouse”.

- Make sure to **select**:
  - “Create a git repository”
  - “Use renv with this project”
  - “Open in new session”
- Make sure to **deselect** (if present)
  - “Use visual markdown editor”
- Now click “Create Project”

<img src="Images/name_quarto_project.png" 
     alt="Image showing how to name new Quarto project, put in Vaske or Treehouse folder, and initialize with renv" 
     style="width: 55%;">

------------------------------------------------------------------------

## What Happens When You Create a New Quarto Project?

Now you have created your first Quarto project in RStudio! But what does
that actually mean? Let’s **exit** RStudio and navigate to your new
project directory to check it out. What do you see? Let’s break it down.

When you create a new Quarto project, RStudio generates:

- The .Rproj file (opens your project in RStudio)
- The renv folder (stores all of the R packages used in your project)
- The renv.lock file (records the *exact* packages & versions)

You *may* see some extra files… you can ignore them, for now.

------------------------------------------------------------------------

## Use GitHub Desktop to Make Your Code Shareable

Since you just created a new project with new files, your GitHub Desktop
should reflect these changes.

Let’s open GitHub Desktop and follow the instructions to add your new
repository (i.e. the directory/workspace your new project exists in.)

1.  Click “Add” –\> “Add Existing Repository…” –\> “Choose…” –\>
    navigate to your new repository –\> “Add Repository”

<img src="Images/add_repo_to_github_desktop.png" 
     alt="GitHub Desktop visual with buttons to add an existing repository" 
     style="width: 55%;">

<img src="Images/find_new_repo.png" 
     alt="GitHub Desktop visual with buttons to add your new local repository" 
     style="width: 55%;">

You will see something similar to the following… Keep in mind some
listed files (on the left of your screen) may be different due to
RStudio version differences.

<img src="Images/new_repo_in_github_desktop.png" 
     alt="GitHub Desktop screen upon adding new repository; files listed on the left" 
     style="width: 55%;">

2.  Push Your New Repository to GitHub

Now that you have your new repository on GitHub Desktop, you can ‘push’
your work to the main branch (**note**: your ‘main’ branch may be called
‘master’, like mine). When you ‘push’ your work to GitHub, you are
sending the changes you made on your local computer to GitHub’s servers,
where others will view and even collaborate on your code.

To push your work main/master:

1.  Save Your Work

- Make sure all files are saved locally. Since you haven’t written any
  new code or text, your changes (your new project files) should be
  saved and ready to be pushed. For future reference, here is the save
  button.

<img src="Images/save_changes.png" 
     alt="Button on RStudio to save changes to current document, circled in red" 
     style="width: 55%;">

2.  In **GitHub Desktop**, Commit Your Changes to Main/Master

- Navigate to the description box and type in the required summary:
  - In this case, you can write something like ‘Created new project with
    renv’
- Click ‘Commit \[some number\] files to main/master’

<img src="Images/push_to_main.png" 
     alt="Screenshot of GitHub Desktop on main/master branch with commit file(s) button in blue" 
     style="width: 55%;">

3.  In **GitHub Desktop**, Publish Your Repository

- Now that your work is committed to the main branch, you can click
  ‘Publish repository’

<img src="Images/publish_repo.png" 
     alt="GitHub Desktop screen after committing changes to main branch. Publish repository button is in blue" 
     style="width: 55%;">

- Make sure you are publishing your new repository to the UCSC Treehouse
  organization on GitHub

<img src="Images/publish_repo_to_treehouse_org.png" 
     alt="GitHub Desktop visual with button to publish your new repository. The organization is changed from 'none' to 'UCSC-Treehouse'" 
     style="width: 55%;">

------------------------------------------------------------------------

## Next Steps:

Now let’s get into some actual data analysis! By the end of Chapter 1,
you will have your first experience with plotting data, as well as
creating branches and pull requests on GitHub.

------------------------------------------------------------------------

**START** with [Chapter
1](https://github.com/UCSC-Treehouse/Essential-skills-for-Treehouse-computational-research/blob/main/Chapter-Instructions/Chapter_01_Instructions.md)
