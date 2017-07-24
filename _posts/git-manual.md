---
title: Git Manual
tags: git
categories: 程序媛|Coding
date: 2016-09-13 19:13:11
updated: 2017-05-25 17:30:00
---


As coding more and more, `git` becomes a must-use tool to save the codes and control versions. 
This article is an incomplete manual for the beginnners. 
In fact, now I still cannot understand the work of `git` very well. Just hope to learn more when using more.

<!--more-->
## Intro
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
```
The system show: 
```
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

### Check the status
```bash
git status
```

### Check the history

```bash
git log
```

## Work with the Remote Repo

Then:
```bash
git pull git://github.com/monkey0105/source.git master
```

The files then are all downloaded in the file. 

Set `origin` address
```bash
git remote add origin https://github.com/monkey0105/source.git
```

#### Method 2

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
Check by:
```bash
git status
```

## Notice

1. Always pay attention to the branch you currently work on. You can use `checkout` to switch between the branches.
```bash
git checkout dev
```

2. Never delete actions which have been already pushed.

## References 

* A helpful tutorial [Pro Git](http://iissnan.com/progit/index.html), translated and editted by the author of theme [`Next`](https://github.com/iissnan/hexo-theme-next). 

