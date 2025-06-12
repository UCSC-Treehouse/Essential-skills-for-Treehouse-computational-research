# Chapter 01c, GitHub


**That was a lot!** Now, to wrap up Chapter 1 you will learn to use
GitHub to make your work viewable to others (and also reproducible!) You
will create a new branch on GitHub and push your Chapter 1 work to that
branch, creating a pull request in the process. This pull request will
allow a mentor to review your work each chapter.

------------------------------------------------------------------------

Remember that Chapter 1 branch you created? After you save your Quarto
notebook, you are going to push all of your new Chapter 1 changes to
that branch on GitHub, so your work will be viewable on the
UCSC-Treehouse organization.

Lastly, you will request your mentor as a ‘reviewer’, so they can check
over your work *before* you officially push it to the main branch.

Navigate back to **GitHub Desktop**… You should see your new changes
highlighted in red.

1.  Push new Chapter 1 changes to chapter-01 branch

<img src="Images/push_ch_1_to_ch_1_branch.png" 
     alt="Cropped image of bottom left corder of GitHub Desktop screen where description 'Create and complete chapter 1' is given and blue 'Commit 1 file to chapter-01' button is visible at the bottom."
     style="width: 55%;">

2.  Publish the new chapter-01 branch

<img src="Images/publish_ch_1_branch.png" 
     alt="GitHub Desktop screen where blue 'Publish branch' button is present and circled in red."
     style="width: 55%;">

3.  Create a pull request

<img src="Images/create_pull_request.png" 
     alt="GitHub Desktop screen after committing changes to new branch, prompted to click 'Create Pull Request' button in blue."
     style="width: 55%;">

You will be relocated to the **GitHub browser**.

1.  Add your mentor as a reviewer (**Note**: here I use ‘hbeale’ but
    make sure you are adding *your* mentor’s GitHub id)

<img src="Images/add_reviewer_to_pull_request.png" 
     alt="GitHub browser screen upon creating a pull request. On the righthand side, a reviewer is added. The example shows 'hbeale' as reviewer, circled in red."
     style="width: 55%;">

Now your mentor can review your work while making some suggested
changes, where needed. After your mentor does this, when you return to
GitHub Desktop and are under the ‘chapter-01’ branch, you will see your
saved, unchanged work in **green** and your mentor’s changes (or any new
changes you have made too) in **red**. The red signifies *any* new
change that has not been committed and pushed to a branch.

After adding their comments, your mentor will accept your pull request
on GitHub. Here’s what happens and what you’ll see:

1.  Pull Request is Merged into the Main Branch

- Your proposed changes (commits) are added to the **main** branch

- GitHub marks the pull request as “Merged”

2.  Branch Deletion

- After the merge, you will be prompted to *safely* delete your
  Chapter-01 branch

3.  Sync Your Local Repository

Navigate to your local copy and check the files. Do you see the new
changes?

While your mentors new edits appear on GitHub after the merge, they will
not be reflected in your *local* repository. To sync these changes to
your local repository, you will need to “pull” them from GitHub.

Open GitHub desktop. You should see a highlighted blue bar prompting you
to “Pull 1 commit from the origin remote”. Click “Pull origin”.

<img src="Images/pull_origin_changes.png" 
     alt="GitHub desktop screen upon changes to a file by someone on GitHub. GitHub desktop prompts the user to 'pull origin', ultimately syncing the remote and local repositories."
     style="width: 55%;">

Now the remote and local repositories are synced! (**Hint**: Anytime you
make changes to either the remote (e.g. on GitHub) or the local
repository, you will need to sync them to keep both versions up to
date.)

------------------------------------------------------------------------

**NEXT UP:** [Chapter
2](https://github.com/UCSC-Treehouse/Essential-skills-for-Treehouse-computational-research/blob/main/Chapter-Instructions/Chapter_02_Instructions.md)
