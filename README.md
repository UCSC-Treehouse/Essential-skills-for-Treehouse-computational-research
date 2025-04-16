# Intro to Computational Research

Welcome to your introductory guide to computational research!

## Background

Here are some (peer reviewed) reasons why computational research matters:

1. Developing computational skills enhances your **computational thinking** (a term coined by Jeannette M. Wing in 2006). It involves "**solving problems** [and] **designing systems**" to address the ultimate question of "what is computable?" [<sup>1</sup>].
2. A preliminary study on code comprehension in a cohort of 57 scientific researchers, 51 of whom hold a PhD, found that "most participants learn programming through **self-study or on-the-job training**, with 57.9% lacking formal instruction" [<sup>2</sup>].
3. **Data and code sharing** is absolutely essential for scientific research. A study in cancer research, observing "306 cancer-related articles indexed in PubMed in 2019" show that 59/306, or only 19%, provided publicly available data [<sup>3a</sup>]. Published and publicly available data and code is imperative for "scientific transparency, reproducibility, reuse, or compliance with funding bodies, journals, and academic institutions" [<sup>3b</sup>]. 

[<sup>1</sup>] Wing, Jeannette. (2006). Computational Thinking. Communications of the ACM. 49. 33-35. 10.1145/1118178.1118215.
[<sup>2</sup>] Chen, Alyssia & Wong, Carol & Sharif, Bonita & Peruma, Anthony. (2025). Exploring Code Comprehension in Scientific Programming: Preliminary Insights from Research Scientists. 10.48550/arXiv.2501.10037.
[<sup>3a</sup>] Hamilton, D. G., Page, M. J., Finch, S., Everitt, S., & Fidler, F. (2022). How often do cancer researchers make their data and code available and what factors are associated with sharing?. BMC medicine, 20(1), 438. https://doi.org/10.1186/s12916-022-02644-2
[<sup>3b</sup>] Trisovic, A., Lau, M.K., Pasquier, T. et al. A large-scale study on research code quality and execution. Sci Data 9, 60 (2022). https://doi.org/10.1038/s41597-022-01143-6

## Goals

- Ease your discomfort with computational research (demystify coding!)
- Use Terminal (bash), RStudio, Visual Studio Code, and Github
- Building repositories, files, and pipelines
- Getting a hang of research computing workflows

## Getting Started

0. Prerequisites
- Create a [GitHub account](https://github.com/signup) with your UCSC email and join UCSC-Treehouse organization
- Install [Git](https://git-scm.com/downloads)
- Install [VS Code](https://code.visualstudio.com/download)
1. Clone this repository (*instructions below*)
2. Explore the modules in order
3. Complete the exercises and mini project

### Cloning a Repository:

To get a local (ie on *your* computer) copy of this project:

0. Follow above prerequisites

1. Open Terminal
- **Windows**: Press `Win + R`, type `cmd`, hit Enter
- **Mac**: Press `Cmd + Space`, type "Terminal", hit Enter
You'll see a blinking cursor like this:
```bash
username@computer-name ~ %
```

2. Create and navigate to new project folder
- first create a folder for Vaske-Lab or Treehouse by copying the following into Terminal
```bash
mkdir Vaske-Lab
cd Vaske-Lab
```
*This makes a "Vaske-Lab" folder in your home directory and enters it*

3. Clone this repository by copying the following into Terminal
```bash
   git clone https://github.com/your-username/Intro-to-Computational-Research.git
   ```
   *replace your-username*
You should see:
```bash
Cloning into 'Intro-to-Computational-Research'...
remote: Enumerating objects: 100, done.
remote: Counting objects: 100% (100/100), done.
Receiving objects: 100% (100/100), 1.23 MiB | 2.45 MiB/s, done.
```

4. Enter the new repository folder by copying the following into Terminal
```bash
cd Intro-to-Computational-Research
```
Your terminal prompt should now show:
```bash
username@computer-name Intro-to-Computational-Research %
``` 
*username and computer-name will be your local computer names*

5. Confirm changes
List all files to confirm cloning worked:
```bash
ls
```
You should see `README.md` and other project files.

#### Troubleshooting
**If you get errors**:
1. `git: command not found` → [Install Git](https://git-scm.com/downloads) first
2. `Permission denied` → Try the HTTPS URL instead of SSH
3. Wrong folder? Run `cd ..` to go back, then retry