---
title: Git Manual
tags: git
categories: 程序媛|Coding
date: 2016-09-13 19:13:11
---


As coding more and more, `git` becomes a must-use tool to save the codes and control versions. 
This article is an incomplete manual for the beginnners. 
In fact, now I still cannot understand the work of `git` very well. Just hope to learn more when using more.

<!--more-->
## Personal notes

### Build local repository

#### Method 1

Create a new folder to hold the clone files, then: 

```bash
git init
```
The system show: 
```
Initialized empty Git repository in /home/katherine/Hexo/source/.git/
```

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
git clone git://github.com/monkey0105/source.git --branch master
```


### Update the remote repository
After do some changes, `commit` and `push` to the remote repository.
```bash
git add .
git commit -m 'name the commit yourself'
```

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

A helpful tutorial [Pro Git](http://iissnan.com/progit/index.html), translated and editted by the author of theme [`Next`](https://github.com/iissnan/hexo-theme-next). 

