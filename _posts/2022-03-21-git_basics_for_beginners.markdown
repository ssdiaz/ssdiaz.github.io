---
layout: post
title:      "Git Basics For Beginners"
date:       2022-03-21 04:54:42 +0000
permalink:  git_basics_for_beginners
---


This blog covers the basic commands you need to know if you’re new to using Git and want to  practice better version control. 

In short, Git is helpful to track project changes and to push those changes to your GitHub Repository. According to [Wikipedia](http://en.wikipedia.org/wiki/Git):

> Git is software for tracking changes in any set of files, usually used for coordinating work among programmers collaboratively developing source code during software development.
> 

For additional resources, check out the [Git Guides](https://github.com/git-guides)! 

## 3 Commands To Update Your GitHub Repository From Your Terminal Directory
To update your GitHub Repository on the website, you need to (1) *add* all your changed files to the next commit, (2) declare a *commit* with a message describing the changes made, and (3) *push* your changed files to GitHub Repo. Remember, it's best to make many small commits so you always have a working version to reference in GitHub, without losing coding progress!

#### git add .
* This command adds all files that were changed to be included into the next commit
* You can add a specific file to the commit queue in place of the period. For example:  `git add README.md`

#### git commit -m "Your message here!"
* This command commits a snapshot of your project and accepts a message 
* The message can say whatever you want, but it should help other developers (and yourself) identify the changes made on this version

#### git push
* This command will send your added files and commit message to GitHub. Your Repo will now reflect those changes!


## Working with Branches
A Branch is a version of your project that you can switch to. In theory, other developers will be working off the same branch while adding in their code. When their code is ready, they will push it to the **main** branch, which you can then clone onto your computer to work off of, and vice versa. The ‘**main**’ branch is exactly that – **the MAIN branch** – and should (in theory) always be working. You want to be able to rely on your **main** branch in case of future errors, so be sure you’re certain about the changes being pushed to the **main** branch before merging. 


#### git branch -a 
* This command shows all your Git branches
* This branch will also tell you what branch you are currently working on

#### git checkout -b YourBranchNameHere
* This command creates a new branch and will automatically switch you to be working on this new branch

#### git checkout YourBranchNameHere
* `git checkout` lets you switch the branch you’re currently working on. The code in the files will change to reflect the branch you switched to.
* For example: `git checkout main` brings you back to working on the **main** branch
* NOTE: Make sure you're back on the **main** branch before you merge any working branches to **main** by using this command 

#### git merge YourBranchNameHere --no-ff
* This command merges whatever branch you write in `YourBranchNameHere` into the current branch you are switched on
* For example, if you are ready to merge the `FixIssue` branch into your **main** branch, make sure you’re working on the **main** branch (use `git branch -a` to see what branch you’re on or use `git checkout main` to switch), then use command: `git merge FixIssue --no-ff` to merge `FixIssue` into **main**.
* The `--no-ff` syntax tells Git to keep all commit messages prior to the merge
* Once you merge, follow the above actions to `git add .` , `git commit -m "YourMessageHere"`, and `git push` your changes up to the  branch in GitHub.

## Other Helpful Commands

#### git log
* To check all your previous git commits

#### git branch -m OldBranchName NewBranchName
* This allows you to rename a branch name

#### git commit --amend
* This allows you to replace the most recent commit with a new commit.  [(See Git Guides)](https://github.com/git-guides/git-commit)

#### git branch -d YourBranchNameHere
* This will delete the specified branch name
* Once merged to the **main** branch, you may no longer need a working branch and may want to delete it with this command
* NOTE: Git will throw an error if you try to delete an unmerged branch

#### git reset --hard CommitLogNumberHere
* Use with caution - this command will delete all your work up until a certain commit
* May be helpful if you need to reset and go back to the previous commit



Happy Coding!

