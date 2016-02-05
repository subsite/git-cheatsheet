# Git Cheatsheet
Yet another git cheatsheet. Pretty universal, but using Bitbucket as example for all upstream repositories. Github works the same way. 
#
[TOC]

## Setup
Create a new repo on Bitbucket, copy-paste the instructions from there or do:
```sh
cd /path/to/your/project
git init 
git remote add origin git@bitbucket.org:me/newrepo.git
nano .gitignore # Do this now if you already have some files. Example below.
git add . # add everything
git commit # Initial commit
git push -u origin --all # First push
```
Whoops, did I set the wrong upstream url?
```
git remote -v # List upstream repos
git remote set-url origin git@bitbucket.org:me/correct-repo.git
```
Make the current branch visible in the prompt (Ubuntu/Debian). In `~/.bashrc` (somewhere before the prompt line) do:
```sh
# Include the git-prompt (this isn't needed on some systems, but safe to do)
source /etc/bash_completion.d/git-prompt

# Show dirty state with * like this: (master *)$
export GIT_PS1_SHOWDIRTYSTATE=1
```
Then add `$(__git_ps1)` to the end of the prompt line, something like:
```sh
PS1='${debian_chroot:[ ... ] \w\[\033[00m\]$(__git_ps1)\$ '
```
Uncomment the line `force_color_prompt=yes` while you're at it.

## Clone
Copy the cloning address from Bitbucket, choose *ssh* for repos you intend to commit to and have uploaded your SSH key to, *https* for repos you only want to use or don't have write permissions to.
```sh
cd /path/to/dir
# ssh:
git clone git@bitbucket.org:me/somerepo.git 
# https:
git clone https://me@bitbucket.org/me/somerepo.git
```
Cloning will create a dir for the repo, no need to do it beforehand. In this case the repo dir will be `/path/to/dir/somerepo/`

## Branches and checkout
```sh
git branch # show branches
git branch --no-merged # show unmerged branches
git checkout my-branch # check out branch "my-branch"
git checkout -b my-branch # create a branch and checkout
git checkout other-branch /path/to/file # check out file from other branch
git branch -d my-branch # delete local branch
git push -u origin my-branch # push local branch to Bitbucket
git for-each-ref --sort=-committerdate refs/heads/ --format='%(committerdate:short) %(authorname) %(refname:short)'
 # show branches with dates of last commit
```
## Commit
```sh
git commit # Commit staged changes
git commit -a # Stage and commit
git commit --amend # Fix last commit, forgot to add files, typo in message etc.
git commit --dry-run # See what will happen
```
## Merging and conflicts
```sh
git pull # fetch and merge from upstream branch
git merge other-branch # merge other branch into current branch
(master|MERGING)$ git merge --abort # restore conflicting merge
```
Fix conflicts manually by editing the file (or using some fancy mergetool), or, if the conflict is simple:
```sh
# Option 1, the correct file is in the other branch:
git checkout --theirs path/to/file
# Option 2, the correct file is in this branch
git checkout --ours path/to/file
# Then:
git commit -a
```
## Ninja style push-pull
When working on a web server with a devel and a production repo side by side, and you get bored with doing `git push` [wait], `cd ../website; git pull` [wait], `cd ../website_devel` with my *push-pull*-script:
https://github.com/subsite/misc-scripts/blob/master/pushpull
It also includes some basic checking that will prevent you from doing something embarrassing.

## Adding (staging)
```sh
git add . # add all new untracked files in current path
git add -u . # add all deleted files in current path
git add -A # add all new untracked and deleted files in current path
```
## Log
```sh
git log --pretty=format:"%h%x09%an%x09%ad%x09%s" # Concise with times
git log # List commits in current branch
git log other-branch # List commits from another branch
git log --follow path/to/file # All commits for specific file
git show HEAD~1:path/to/file # Display file contents one commit back
```
## Diff
```sh
git diff # Show changes since last commit
git diff my-branch other-branch # Show diff between branches
git diff other-branch -- path/to/file # Specific file
git diff --name-only my-branch other-branch # Show filenames of differing files
git diff --name-status other-branch # List files with differences in another branch
git cherry -v # Show unpushed commits
```
## Stash
Stashing is useful when you want to temporary clean up modified files in your repo.
```sh
git stash # stashes uncommitted files, makes working directory clean
git stash apply # applies the stash to the current branch
git stash branch newbranch # short for stash, checkout new branch, apply
git stash list # List stashes
git stash apply stash@{2} # Apply second stash in list
git stash show -p | git apply -R # un-apply last applied stash
git stash show -p stash@{0} | git apply -R" # un-apply specific stash
```
## Undoing and restoring
```sh
git log # List commits and get keys (eg. 835a0edccd) to copy-paste
git show HEAD~1:path/to/file # Show file of last commit
git checkout 835a0edccd # Check out a commit
git reset --hard 835a0edccd # Reset git to the situation of this commit
git checkout 835a0edccd -- path/to/file # Get file from this commit, then: git commit
git checkout 835a0edccd^ -- path/to/file # Restore file deleten in this commit. Note the caret (^)
git merge --abort # restore conflicting merge
git reset #	unstage added files
git stash # Discard uncommited changes
git cherry-pick 2e744aba6c # pick a specific commit from another branch
```
## Aliases
Put aliases for useful commands in `~/.gitconfig`
```sh
[alias]
        branchdiff = diff --name-status
        prettylog = log --pretty=format:"%h%x09%an%x09%ad%x09%s"
        branchlog = for-each-ref --sort=-committerdate refs/heads/ --format='%(committerdate:short) %(authorn$
```
## Gitignore
Some basic excludes, save as `.gitignore` in your repo root.
```sh
*.orig
*.bak
*.log
*~
# npm projects
/node_modules
# Visual Studio Code metadata
/.vscode
# Railo or Coldfusion metadata
/WEB-INF
# For TypeScript projects like Angular2
/app/**/*.js
/app/**/*.map
```