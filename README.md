# Git Cheatsheet
Yet another git cheatsheet.

 * [Git Cheatsheet](#git-cheatsheet)
    * [Branches and checkout](#branches-and-checkout)
    * [Adding (staging)](#adding-staging)
    * [Commit](#commit)
    * [Merging](#merging)
      * [Dealing with conflicts](#dealing-with-conflicts)
      * [Merge keeping certain files](#merge-keeping-certain-files)
    * [Log](#log)
    * [Diff](#diff)
    * [Stash](#stash)
    * [Undoing and restoring](#undoing-and-restoring)
    * [Tags](#tags)
    * [Setup](#setup)
      * [Create new repository](#create-new-repository)
      * [Clone an existing repository](#clone-an-existing-repository)
      * [Local environment](#local-environment)
      * [Gitignore](#gitignore)
      * [Ninja style push-pull](#ninja-style-push-pull)
      * [Aliases](#aliases)



## Branches and checkout
```sh
git branch # show branches
git branch --no-merged # show unmerged branches
git checkout my-branch # check out branch "my-branch"
git checkout -b my-branch # create a branch and checkout
git checkout other-branch /path/to/file # check out file from other branch
git branch -d my-branch # delete local branch
git push -u origin my-branch # push local branch upstream
git for-each-ref --sort=-committerdate refs/heads/ --format='%(committerdate:short) %(authorname) %(refname:short)'
 # show branches with dates of last commit
```
## Adding (staging)
```sh
git add path/file1 path/file2 # add these files
git add . # add all new untracked files in current path
git add -u . # add all deleted files in current path
git add -A # add all new untracked and deleted files in current path
git reset #	unstage all added files
```
## Commit
```sh
git commit # Commit staged changes
git commit -a # Stage and commit
git commit --amend # Fix last commit, forgot to add files, typo in message etc.
git commit --dry-run # See what will happen
git cherry -v # List unpushed commits
git push # Push all unpushed commits upstream
```
## Merging
```sh
git pull # fetch and merge from upstream branch
git merge other-branch # merge other branch into current branch
git checkout other-branch path/file1 path/file2 # pick specific files from another branch
git merge --strategy-option ours other-branch # Automatically resolve conflicts with our version, "theirs" for the other
branch
git merge --squash other-branch # squash all commits to one big before merge. Don't use if you intend to merge back... 
```
### Dealing with conflicts


Fix conflicts manually by editing the file (or using some fancy merge tool), or, if the conflict is simple:
```sh
# Option 1, the correct file is in the other branch:
git checkout --theirs path/to/file
# Option 2, the correct file is in this branch
git checkout --ours path/to/file
# Option 3
git merge --abort # Abort the merge, then
git merge --strategy-option ours other-branch # Automatically resolve conflicts to "ours" (or "theirs")
# Finish the merge by adding and committing
```
I changed my mind, I don't want to merge yet:
```sh
(master|MERGING)$ git merge --abort # abort merge process, revert to pre-merge situation
```
### Merge keeping certain files
Ok, so you want to merge in another branch, but you have some content you want to keep:

First do
```
git config --global merge.ours.driver true # Adds properties to ~/.gitconfig
```
The create the file `.gitattributes` in your repo, adding for example:
```sh
#file: .gitattributes
content/** merge=ours
app/app.html merge=ours
.gitignore merge=ours
```
Now you can merge without these files being overwritten.

## Log
```sh
git log --pretty=format:"%h%x09%an%x09%ad%x09%s" # Concise with times
git log # List commits in current branch
git log other-branch # List commits from another branch
git log --follow path/to/file # All commits for specific file
git show HEAD~1:path/to/file # Display file contents one commit back
git show 835a0edccd:path/to/file # Display file contents specific commit
```
## Diff
```sh
git diff # Show changes since last commit
git diff my-branch other-branch # Show diff between branches
git diff other-branch -- path/to/file # Specific file
git diff --name-status other-branch # List files with differences in another branch
git cherry -v # List unpushed commits
```
## Stash
Stashing is useful when you want to temporary clean up modified files in your repo.
```sh
git stash # Stash uncommitted files, clean up working directory 
git stash apply # Apply the stash to the current branch
git stash branch newbranch # Short for stash, checkout new branch, apply
git stash list # List stashes
git stash apply stash@{2} # Apply second stash in list
git stash show -p | git apply -R # Un-apply last applied stash
git stash show -p stash@{0} | git apply -R # Un-apply specific stash
```
## Undoing and restoring
Git is fantastic for undoing things and restoring from its unlimited history, but only if you've told git about the changes you've made. Commit frequently!

**Getting pre-restore info**
```sh
git log # List commits and get keys (eg. 835a0edccd) to copy-paste
git log --follow path/to/file # All commits for specific file
git show 835a0edccd:path/to/file # Display file contents specific commit
```
**Unstaged files**
```sh
git reset #	unstage added files
git stash # Discard uncommited changes
```
**Restore specific files**
```sh
git checkout 835a0edccd -- path/to/file # Get file from this commit, then: git commit
git checkout 835a0edccd^ -- path/to/file # Restore file deleted in this commit. The caret (^) means "as it was before committing", e.g. before the file was deleted
git checkout other-branch path/file1 path/file2 # pick specific files from another branch
```
**Revert to or restore a specific commit**
```sh
git cherry-pick 2e744aba6c # pick a specific commit from another branch
git revert 835a0edccd # Revert to this commit without losing your history. Safest way to restore.
git revert --abort # As with merge --abort, this lets you abort the process if it doesn't look good
git checkout -b newbranch 835a0edccd # Check out a new branch created from this commit
git reset --hard 835a0edccd # Reset to this commit. Removes subsequent commits, use revert if unsure.
```
**Revert to upstream situation **
```sh
git fetch origin # fetch everything from upstream 
git reset --hard origin/master # Reset to this

```
## Tags
Tags are good for release version numbers.
```sh
git tag # list tags
git tag -a v1.4 -m "My version 1.4"
git push --tags # push tags, pull --tags to pull
```
For npm projects, there's a cool auto versioning tool that both creates a git tag and updates the version of package.json. See https://docs.npmjs.com/cli/version
```sh
npm install version
npm version patch -m "My new patch" # Bump the patch version e.g. 1.4.1 => 1.4.2 ( patch | minor | major )
npm version v1.5.0 -m "My new version %s" # Bump the version explicitly. %s is a mask for the version
```
## Setup 

### Create new repository 
Create the new repo on Bitbucket/Github, copy-paste the instructions from there or do:
```sh
cd /path/to/your/project
git init 
git remote add origin git@github.com:me/newrepo.git
nano .gitignore # Do this now if you already have some files. Example below.
git add . # add everything. Too many files? git reset, edit .gitignore, add again
git commit # Initial commit
git push -u origin --all # First push
```
Whoops, did I set the wrong upstream url?
```
git remote -v # List upstream repos
git remote set-url origin git@github.com:me/correct-repo.git
```
### Clone an existing repository
Copy the cloning address from Github/Bitbucket, choose *ssh* for repos you intend to commit to and have uploaded your SSH key to, *https* for repos you only want to use or don't have write permissions to. 
```sh
cd /path/to/dir
# ssh:
git clone git@github.com:me/somerepo.git 
# https:
git clone https://me@github.com/me/somerepo.git
```
Cloning will create a directory for the repo, so do this from the path you want the repo to reside in.

Clone just one remote branch into a new repository
```
git clone git@github.com:me/somerepo.git -b some-branch --single-branch /path/to/new_repo
```
### Local environment
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
### Gitignore
Some basic excludes, save as `.gitignore` in your repo root.
```sh
*.orig
*.bak
*.log
*~
# npm projects
node_modules
bower_components
# Visual Studio Code metadata
.vscode
# Railo or Coldfusion metadata
WEB-INF
# For TypeScript projects like Angular2
app/**/*.js
app/**/*.map
```
Sometimes you want to start ignoring files you've earlier tracked with git. Adding a line to gitignore won't be enough in this case, you also have to remove it from git's index with:
```
git rm --cached file/to/ignore
git rm -r 'app/*.js' # recursive wildcard, note single quotes
```
The `--cached` flag means "remove from git index only, don't remove from filesystem".

### Ninja style push-pull
When working on a web server with a devel and a production repo side by side, and you get bored with doing this all the time:
```sh
www/site_devel (master)$ git push
# wait...
www/site_devel (master)$ cd ../site
www/site (master)$ git pull
# wait...
www/site (master)$ cd ../site_devel/
```
Instead, you could use my *push-pull*-script, and simply do:
```sh
www/site_devel (master)$ git pushpull
```
The script shows which commits will be pushed and checks that you are in the correct dir and branch. The script is located at: https://github.com/subsite/misc-scripts/blob/master/pushpull

### Aliases
Put aliases for useful commands in `~/.gitconfig`
```sh
[alias]
        branchdiff = diff --name-status
        prettylog = log --pretty=format:"%h%x09%an%x09%ad%x09%s"
        branchlog = for-each-ref --sort=-committerdate refs/heads/ --format='%(committerdate:short) %(authorname) %(refname:short)'
```
