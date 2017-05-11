---
title: Download Bing Background Image Everyday by Python
tags:
  - python
  - reptile
  - urllib
  - crontab
categories: 程序媛|Coding
date: 2016-06-16 21:27:25
---

Recently I read a translation version of a Quora Q&A in which people are talking about how the programmers use computer to serve their own life.  
The translation version is: [看程序员是如何把自动化做到极致的！](http://blog.jobbole.com/100744/)  
And the initial Quora post is: [As a programmer, what tasks have you automated to make your everyday life easier?](https://www.quora.com/As-a-programmer-what-tasks-have-you-automated-to-make-your-everyday-life-easier)  

One engineer just mentioned he wrote a small program to download [Bing](http://cn.bing.com/) background images everyday automatically by the computer. The images, which would be a new one everyday, are all great photographs token all over the world.  

<!--more-->

Recently, I start learning and using Python in my work. And I heard a lot about Python's fantastic functionality in collecting data from the Internet.  
So I think maybe I could do this as well.  

A post on [知乎](https://www.zhihu.com/) can be a good intro on Python reptile for us beginners - [如何入门 Python 爬虫？](https://www.zhihu.com/question/20899988). 

## See the Pictures

Now the program has been run for 3 times.  

Following are the results! 

{% asset_img OrienteStation_ZH-CN8775637045_1920x1080.jpg image1 %}

{% asset_img PerceRock_ZH-CN12739516630_1920x1080.jpg image2 %}

{% asset_img CanyonlandsNP_ZH-CN12598047863_1920x1080.jpg image3 %}

Look great! Right?  

Now let's start to do the job!  

## First, Get the Picture

This time, I use `urllib.request` module.  

``` python
import urllib.request
url = 'http://cn.bing.com/'  # Set url as Bing homepage
page = urllib.request.urlopen(url)
html = page.read().decode('utf-8')  # Get the raw HTML code of the page

# Get the url of the picture itself
# Following rules need to be recognised beforehand manually
a = html.find(r'g_img={url: "http:')
b = html.find(r'_1920x1080.jpg')

path = "/home/katherine/Pictures/"  # Set the target position to save the picture
img_url = html[(a+13):(b+14)].replace('\\','')  # Replce the '\\'s
name = img_url.rsplit('/')[-1]  # Use the raw name
conn = urllib.request.urlopen(img_url)
f = open(path+name,'wb')
f.write(conn.read())
f.close()  # Save the picture data
print('Pic Saved!')
```

## Next, Ask PC to do Everyday Job 
### `crontab`

I searched and people say `crontab` could solve the problem and it's already in Linux os.  

Syntax is as below: 

```
crontab [-u user] file
crontab [ -u user ] [ -i ] { -e | -l | -r }

-e	(edit user's crontab)
-l	(list user's crontab)
-r	(delete user's crontab)
-i	(prompt before deleting user's crontab)
```

Below is the syntax of `crontab`. 
```
*   *   *   *   *   command to be executed
-   -   -   -   -
|   |   |   |   |
|   |   |   |   +----- day of week (0 - 6) (Sunday=0)
|   |   |   +------- month (1 - 12)
|   |   +--------- day of month (1 - 31)
|   +----------- hour (0 - 23)
+------------- min (0 - 59)
```

However, I failed...  
Now I think maybe because I didn't specify the user name.  

### `python-crontab`

Then I just find a python package called [`crontab`](https://pypi.python.org/pypi/python-crontab/) which can do the same job.  

Use `pip install python-crontab` to complete the installation. 

``` python
from crontab import CronTab

cron = CronTab(user=True)

job = cron.new(command='python3 /home/katherine/PycharmProjects/untitled/bing_img.py')
job.minute.on(45)
job.hour.on(9)

cron.write()
```

After setting a crontab task, we can use `crontab -l` in Terminal to check.  

Now we can see 
```
45  9  *  *  *  python3 /home/katherine/PycharmProjects/untitled/bing_img.py
```
which means 09:45 everyday, `python3` would run `bing_img.py`.  


**If the computer is power off, the `crontab` will not work.**   

## Next Step

Maybe in next step I can collect the description for the image everyday because I can tell no information only by looking at the beautiful pictures.   
Or I could try other interesting tasks ;P  

