---
title: Hello World
date: 2015-12-29 16:52:09
categories: 拿来主义|Copinism
tags: 
  - Hexo
  - markdown
---
Just keep this post as a memo about Hexo, markdown gramma, etc. 

<!--more-->
## Markdown
* [Cheating Sheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

## Hexo
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).
Open Git Bash and turn my working directory to D:/Hexo. 
``` bash
$ cd d:/Hexo
```
### Create a new draft
The reference is http://oomusou.io/hexo/hexo-draft/. 
Create a new draft under the directory `source/_drafts`. 
```bash
$ hexo new draft <title>
```
After editing the drafts, use `--draft` parameter to preview. 
``` bash
$ hexo server --draft
```
After being satisfied with the draft, use `publish` command to move the `.md` file to the directory `source/_posts`. Then you can `generate` and `deploy`. 
``` bash
$ hexo publish <filename>
```
### Create a new post
``` bash
$ hexo new "My New Post"
```
More info: [Writing](https://hexo.io/docs/writing.html)
### Run server
``` bash
$ hexo server
```
More info: [Server](https://hexo.io/docs/server.html)
### Generate static files
``` bash
$ hexo generate
```
More info: [Generating](https://hexo.io/docs/generating.html)
### Deploy to remote sites
``` bash
$ hexo deploy
```
More info: [Deployment](https://hexo.io/docs/deployment.html)
