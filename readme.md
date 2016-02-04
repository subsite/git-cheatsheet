# Fredde's Git Cheatsheet
Pretty universal, but leaning in favor of Bitbucket, Ubuntu and server side web development.
### Setup
Create a new repo on Bitbucket, copy-paste the instructions from there or do:
```sh
cd /path/to/your/project
git init 
git remote add origin git@bitbucket.org:me/newrepo.git
git add . # add everything
git commit # Initial commit
git push -u origin --all # First push
```
Make the current branch visible in the prompt (Ubuntu/Debian). In `~/.bashrc` (somewhere before the prompt line) do:
```sh
source /etc/bash_completion.d/git-prompt
```
Then add `$(__git_ps1)` to the end of the prompt lines, something like:
```sh
PS1='${debian_chroot:[ ... ] \w\[\033[00m\]$(__git_ps1)\$ '
```
Uncomment the line `force_color_prompt=yes` while you're at it.

### Clone
Copy the cloning address from Bitbucket, choose *ssh* for repos you intend to commit to and have uploaded your SSH key to, *https* for repos you only want to use or don't have write permissions to.
```sh
cd /path/to/dir
# ssh:
git clone git@bitbucket.org:me/somerepo.git 
# https:
git clone https://me@bitbucket.org/me/somerepo.git
```
Cloning will create a dir for the repo, no need to do it beforehand. In this case the repo dir will be `/path/to/dir/somerepo/`

### Branches and checkout
```sh
# show branches:
git branch 

# show branches with dates of last commit:


git branch --no-merged # show unmerged branches
git checkout my-branch # check out branch "my-branch"
git checkout -b my-branch # create a branch and checkout
git checkout other-branch /path/to/file # check out file from other branch
git branch -d my-branch # delete local branch
git push -u origin my-branch # push local branch to Bitbucket
```
### Merging and conflicts

```sh
(master)$ git merge my-branch # merge my-branch into master
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
### Adding (staging)
```sh
git add . # add all new untracked files in current path
git add -u . # add all deleted files in current path
git add -A # add all new untracked and deleted files in current path
```
### Log
```sh
git log --pretty=format:"%h%x09%an%x09%ad%x09%s" # Concise with times
git log # List commits in current branch
git log other-branch # List commits from another branch
git log --follow path/to/file # All commits for specific file
git show HEAD~1:path/to/file # Display file contents one commit back
```
### Diff
```sh
git diff # Show changes since last commit
git diff my-branch other-branch # Show diff between branches
git diff other-branch -- path/to/file # Specific file
git diff --name-only my-branch other-branch # Show filenames of differing files
git diff --name-status other-branch # List files with differences in another branch
git cherry -v # Show unpushed commits
```
### Stash
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
### Undoing and restoring
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
### Aliases
Put aliases for useful commands in `~/.gitconfig`
```sh
[alias]
        branchdiff = diff --name-status
        prettylog = log --pretty=format:"%h%x09%an%x09%ad%x09%s"
```
