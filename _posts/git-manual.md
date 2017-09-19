---
title: Git Manual
tags: git
categories: 程序媛|Coding
date: 2017-08-09 14:30:00
---

As coding more and more, `git` becomes a must-use tool to save the codes and control versions. 
This is only a memo for myself to help with my daily work. If it's also helpful to you, I'd be very happy. 

If you lack of basic knowledge about git, I would recommend you a helpful tutorial [Pro Git](http://iissnan.com/progit/index.html), translated and editted by the author of theme [`Next`](https://github.com/iissnan/hexo-theme-next). 

<!--more-->
# Intro
Git is a local version control tool to help users, whether the user is a programmer of a writer, to manage the contents and changes in **text files**. 
If you would like to have a backup remotely, you could use [Github](https://github.com/) to save your work on the remote repo. 

## Install
If you use Ubuntu, `apt-get` could be used to install git. 
```bash
sudo apt-get install git git-core
```

## Work Locally

### Build local repository

To create a new folder to hold the clone files, or you already have some work to track, you can use: : 

```bash
git init
Initialized empty Git repository in /<file_dir>/.git/
```
### Track the work

After initialization, after some work, use
```bash
git add <file path and name>
```
to add the specific file to the track; use
```bash
git add .
```
or
```bash
git add --all
```
to add all changed files to the track. 

### `.gitignore`
If some specific type of files are not needed to track all the time, they can be added to the `.gitignore` file. Just create a file named `.gitignore` and then add the file types or names not necessary to track as the following. Then git will automatically ignore the files listed in the `.gitignore` file. 
```
*.csv # all the csv files
...
```

### commit
```bash
git commit -m '<name the commit yourself>'
```
Or you can use 
```bash
git commit -am '<name the commit yourself>'
```
to combine `add` and `commit` into one sentence. 
__Pay attention, `git commit -am` only commit the files already in the track, and ignore the files which have never been added into the track. __

### Check the status
```bash
git status
```

### Check the history

```bash
git log
```

## Work with the Remote Repo
### If already run `git init`
Then:
```bash
git pull git://github.com/monkey0105/source.git master
```

The files then are all downloaded in the file. 

Set `origin` address
```bash
git remote add origin https://github.com/monkey0105/source.git
```

### If not run `git init`

Just use `git clone`, and `git` will create a new folder to hold the clone files for you.
```bash
git clone git://github.com/monkey0105/source.git --branch <branch> <directory name>
```

`--branch <branch>` can specify the branch you want. if no branch is specified, the default branch would be `master`.

`<directory name>` can specify the place you want to put the repo. if no directory name is specified, the name of the repo would be used as the folder's name. 

### Update the remote repository

After do some changes, `commit` and `push` to the remote repository.


Then:
```bash
git push origin master
```
The files in the Github is up-to-date. 

# Branch

Always pay attention to the branch you currently work on. 

## Check which branch is the work currently on

```bash
git branch
* dev
  master
  ...
```

All the local branches would be shown, and one `*` is in front of the name of current branch. 

## Checkout

You can use `checkout` to create or switch between the branches.

```bash
# Switch to an existed branch <branch name>
git checkout <branch name>

# Create a new branch <branch name> and switch to it
git checkout -b <branch name>
```

## Merge

`git checkout` to the target branch `<branch name 1>` and merge contents on `<branch name 2>` into it. 

```bash
git checkout <branch name 1>
git merge <branch name 2>
```

# Fork

## Sync a Fork Repo

1. Open Terminal.

2. List the current configured remote repository for your fork.

   ```bash
   git remote -v
   origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
   origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
   ```

3. Specify a new remote *upstream* repository that will be synced with the fork.

   ```bash
   git remote add upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git
   ```

4. Verify the new upstream repository you've specified for your fork.

   ```bash
   git remote -v
   origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
   origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
   upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (fetch)
   upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (push)
   ```

5. Fetch the branches and their respective commits from the upstream repository. Commits to `master` will be stored in a local branch, `upstream/master`.

   ```bash
   git fetch upstream
   remote: Counting objects: 75, done.
   remote: Compressing objects: 100% (53/53), done.
   remote: Total 62 (delta 27), reused 44 (delta 9)
   Unpacking objects: 100% (62/62), done.
   From https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY
    * [new branch]      master     -> upstream/master

   ```

6. Check out your fork's local `master` branch.

   ```bash
   git checkout master
   Switched to branch 'master'

   ```

7. Merge the changes from `upstream/master` into your local `master` branch. This brings your fork's `master` branch into sync with the upstream repository, without losing your local changes.

   ```bash
   git merge upstream/master
   Updating a422352..5fdff0f
   Fast-forward
    README                    |    9 -------
    README.md                 |    7 ++++++
    2 files changed, 7 insertions(+), 9 deletions(-)
    delete mode 100644 README
    create mode 100644 README.md

   ```

   If your local branch didn't have any unique commits, Git will instead perform a "fast-forward":

   ```bash
   git merge upstream/master
   Updating 34e91da..16c56ad
   Fast-forward
    README.md                 |    5 +++--
    1 file changed, 3 insertions(+), 2 deletions(-)

   ```

8. **Syncing your fork only updates your local copy of the repository. To update your fork on GitHub, you must [push your changes](https://help.github.com/articles/pushing-to-a-remote).**

# Cancel or Edit Commit in Private Branches

Never reset/rebase actions which have been made in **public** branches.

> Once you understand what rebasing is, the most important thing to learn is when *not* to do it. 
>
> The golden rule of `git rebase` is to never use it on *public* branches.

## Revert, Reset & Checkout Usage in Commit & File Level

Below is the summary. Detail article can be found in [Atlassian Git Tutorial](https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting). 

| Command      | Scope        | Common use cases                         |
| ------------ | ------------ | ---------------------------------------- |
| git reset    | Commit-level | Discard commits in a private branch or throw away uncommited changes |
| git reset    | File-level   | Unstage a file                           |
| git checkout | Commit-level | Switch between branches or inspect old snapshots |
| git checkout | File-level   | Discard changes in the working directory |
| git revert   | Commit-level | Undo commits in a public branch          |
| git revert   | File-level   | -                                        |



## Merging vs. Rebasing

You can find detailed explanation [here](https://www.atlassian.com/git/tutorials/merging-vs-rebasing). 

If you would prefer a clean, linear history free of unnecessary merge commits, you should reach for `git rebase`instead of `git merge` when integrating changes from another branch.

On the other hand, if you want to preserve the complete history of your project and avoid the risk of re-writing public commits, you can stick with `git merge`. Either option is perfectly valid, but at least now you have the option of leveraging the benefits of `git rebase`.

# References 

* A helpful tutorial Pro Git
  * https://git-scm.com/book/zh/v2
  * http://iissnan.com/progit/index.html
* [GitHub Help](https://help.github.com/)
* [A Git Tutorial Powered by Atlassian](https://www.atlassian.com/git/tutorials)

