---
title: Install Operation System Ubuntu 14.04 and Some Softwares
categories: '程序媛|Coding'
tags:
  - ubuntu
  - postgresql
  - python
  - cx_oracle
  - google
  - git
date: 2016-10-17 00:00:00
---

Recently I was working with postgresql and python on Ubuntu. When I need to use pl/python extension, I met a system error but I cannot know how to fix it directly. Then a simple idea just came into my mind: reinstall the operation system and all the softwares.

本文方法也基本适用其他版本的ubuntu。自从装了16以来，bug不断，还是决定重回14了。被自己的强迫症打脸了。
<!--more-->
在16中碰到的尚未解决的问题包括但不限于：
运行pgadmin3时出现：

> Debug: Failed to connect to session manager: SESSION_MANAGER environment variable not defined. 
> Debug: Adding duplicate image handler for 'PNG file'

最开始只有第二句，后来两句都有了。我在自己的电脑上测试少量数据，删除表格或schema会显示Running，没有任何错误提示，也没有任何会跑完的迹象。
在pgadmin3界面点击一个表格，整个软件就会奔溃。
未找到解决方法。


# Install Ubuntu

Go to the [official website](http://www.ubuntu.com/download/desktop) of Ubuntu to get the latest desktop version. Follow the steps and you'll get it. 
If you want previous version like 14.04 LTS, please go as: `Downloads` - `Alternative Downloads` - `Other Images` - `See all ubuntu mirrors`. And then click `http` in Shanghai Jiao Tong University, China. The download speed is quite fast. 

## Prepare a new USB stick

Find `Startup Disk Creator` in the Software Center. If you find the usb disk cannot be erased, go to `Disks` software to format it. 
After you build the usb as a startup disk, it cannot be used to save files any more. 

## Small problem
{% blockquote %}
Missing parameter in configuration file. Keyboard: path
gfxboot.c32: not a COM32R image
boot: 
{% endblockquote %}

The bug would appear only when you build a `Startup Disk` and use it immediately on the same PC.  
Just typing `Tab` and then choosing `live` or `live-install` would start the installation.  

# Open Here in Terminal
This is a very useful add-on called `nautilus` to help daily usage. 
```bash
sudo apt-get install nautilus-open-terminal
```
Then restart nautilus to make it work immediately.
```bash
nautilus -q
```
Then you'll find `Open in Terminal` in your right-click menu list.

# Python
## Install latest python
Since Ubuntu 14.04 is installed with Python 3.4, let's install latest python 3.5.2. 

### Alternative
We need C compiler and other stuff to compile Python
```
sudo apt-get install build-essential
```

SQLite libs need to be installed in order for Python to have SQLite support.
```
sudo apt-get install libsqlite3-dev
sudo apt-get install sqlite3 # for the command-line client
sudo apt-get install bzip2 libbz2-dev
```

### Use apt-get install
添加 PPA：
```bash
sudo add-apt-repository ppa:fkrull/deadsnakes
sudo apt-get update
```

安装 Python 3.5：
```bash
sudo apt-get install python3.5
sudo apt-get install python3.5-dev
sudo apt-get install libncurses5-dev
```

安装新版pip：
```bash
wget https://bootstrap.pypa.io/get-pip.py
sudo python3 get-pip.py
sudo pip3 install setuptools --upgrade
sudo pip3 install ipython[all]
```

Reference:
[在 Ubuntu 14.04.3 上安装 Python 3.5](https://plumz.me/archives/2333/)

## Build virtual env of Python

```bash
sudo apt-get install build-essential
sudo pip3 install --upgrade pip
sudo pip3 install --upgrade virtualenv
virtualenv -p /usr/bin/python3.5 /home/katherine/venv/py35
source /home/katherine/venv/py35/bin/activate
```

Then
```bash
sudo gedit ~/.bashrc
```
Add the following code at the bottom of `.bashrc` file.
```
alias venvpy35='source /home/katherine/venv/py35/bin/activate'
```
Return to the terminal window.
```bash
source ~/.bashrc
venvpy35
```
Next time, you don't need to type the long sentence any more. Instead, type `venvpy35` in the terminal and the virtual environment would be activated.

If you want to exit the virtual environment, just type `deactivate`. 

**`virtualenv` cannot be build under `sudo`, or `pip install` would not have the access to the `site-packages` directory of the `virtualenv`.**

## Install Mostly-Used Packages
```bash
pip3 install pandas
pip3 install psycopg2
pip3 install sqlalchemy==1.1.1
```

## pip-Uninstall Packages with Dependencies
### install pip-autoremove
```bash
pip install pip-autoremove
```
### remove "somepackage" plus its dependencies:
```bash
pip-autoremove somepackage -y
```

Reference: 
[pip-uninstall packages with dependencies](http://stackoverflow.com/questions/7915998/does-uninstalling-a-package-with-pip-also-removes-the-dependent-packages)

# Install cx_oracle

## Install Dependencies
**Activate your virtual environment, and then do the following steps.**
```bash
sudo apt-get install unzip python3.5-dev libaio-dev
```
建议安装对应版本的`python-dev`如`python3.5-dev`，否则`pip install cx_Oracle`会出现错误提示`cannot find Python.h`。

## [Download Oracle Client](http://www.oracle.com/technetwork/topics/linuxx86-64soft-092277.html)
Login oracle website and download: 
+ instantclient-basic-linux.x64-12.1.0.2.0.zip  
+ instantclient-sdk-linux.x64-12.1.0.2.0.zip  
+ instantclient-sqlplus-linux.x64-12.1.0.2.0.zip  

## Unzip oracle client
```bash
cd
mkdir cx_Oracle
unzip instantclient-basic-linux.x64-12.1.0.2.0.zip -d cx_Oracle
unzip instantclient-sdk-linux.x64-12.1.0.2.0.zip -d cx_Oracle
unzip instantclient-sqlplus-linux.x64-12.1.0.2.0.zip -d cx_Oracle
```
## Set ORACLE_HOME location
```bash
sudo gedit ~/.bashrc
```
然后添加以下语句到`~/.bashrc`，然后保存。这是告诉安装程序oracle安装文件的位置，若后面出现`cannot locate oracle installation`的提示，一定是这里没有设置好。注意：ORACLE_HOME 要指向绝对路径。
```bash
# cx_Oracle
export ORACLE_HOME=/home/katherine/cx_Oracle/instantclient_12_1
export LD_LIBRARY_PATH=$ORACLE_HOME:$LD_LIBRARY_PATH
export PATH=${PATH}:${ORACLE_HOME}
```
记得更新配置：
```bash
source ~/.bashrc
mypy35
```
对以下文件建立软链接。
```bash
cd cx_Oracle/instantclient_12_1
sudo ln -sf libclntsh.so.12.1 libclntsh.so
sudo ln -sf libclntshcore.so.12.1 libclntshcore.so
sudo ln -sf libocci.so.12.1 libocci.so
```
## Install cx_oracle
```bash
pip3 install cx_Oracle
```

Reference: 
1. [cx_Oracle在virtualenv(python)中的pip安装](http://aguegu.net/?p=1838)
2. [cx_Oracle instant client install python3](http://openwares.net/database/oracle-instant-installpython3-cx_oracle.html)
3. [在PostgreSQL中使用oracle_fdw访问Orcale(foreigh table)](http://feed.askmaclean.com/archives/%e5%9c%a8postgresql%e4%b8%ad%e4%bd%bf%e7%94%a8oracle_fdw%e8%ae%bf%e9%97%aeoracle.html)

# Install 

# Install postgresql
## Install postgresql
### Add PostgreSQL Apt Repository
```bash
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -
```

### Install PostgreSQL
```bash
sudo apt-get update
sudo apt-get install postgresql-9.5 postgresql-contrib-9.5 postgresql-plpython3-9.5 postgresql-client-9.5 postgresql-common postgresql-server-dev-9.5
sudo apt-get install libpq-dev
```

### Configure PYTHONPATH to PostgreSQL Environment
Add `PYTHONPATH='/home/katherine/Documents/Coding/jfds/src:/home/katherine/venv/py352/lib/python3.5/site-packages'` to `/etc/postgresql/9.5/main/environment`. 

Then run:
```bash
sudo service postgresql restart
```

## Initial settings
`postgreSQL` automatically create a `ubuntu` and `postgreSQL` user named `postgres`. 
First, let's change the password of `postgres`. 
```
sudo su
passwd postgres
```
Then let's login postgreSQL as user `postgres`. 
```
sudo su postgres
psql
```

Then `postgres=# ` would appear in the very front. 
Then let's create a user we would use in our daily work. The same as your ubuntu user account is quite good. 
Creating as `SUPERUSER` is necessary for configuring python fucntions in postgreSQL by `pl/python`. 
```
create user katherine superuser password 'houzi0105';
create database bigdata;
alter database bigdata owner to katherine;
\q
```
Next time, you can use the sentence below to login. 
```
psql -U [dbuser] -d [db] -h 127.0.0.1 -p 5432
```
The abbreviated sentence can be: 
```
psql bigdata
```

The initial settings have been done. 

## plpython3u

```bash
sudo gedit /etc/postgresql/9.5/main/environment
```
```
PYTHONPATH='/home/katherine/Documents/Coding/jfds/src:/home/katherine/venv/py35/lib/python3.5/site-packages'
```
```bash
sudo apt-get install postgresql-plpython3
```
Login postgresql and run:
```
CREATE EXTENSION plpython3u;
```

Reference: 
[How to install 3rd party module for postgres pl/python](https://stackoverflow.com/questions/8032293/how-to-install-3rd-party-module-for-postgres-pl-python/8036067#8036067)
[PostgreSql 下安装 plpython](http://www.ilizhen.com/2015/04/postgresql-%e4%b8%8b%e5%ae%89%e8%a3%85-plpython-ubuntu/)

## Install psycopg2 and sqlalchemy
### Install the Dependencies
```bash
sudo apt-get install python-psycopg2
```
### Then run this command in your virtualenv
```bash
pip install psycopg2
pip install sqlalchemy==1.1.1
```
Reference: [pip 安装特定版本的 Python 包](http://toy.linuxtoy.org/2013/12/11/installing-specific-package-versions-with-pip.html)

# [Sougou Input](http://pinyin.sogou.com/linux/)
## Installation
若是ubuntu14或以上系统，只要双击下载的`.deb`文件，点击`install`，之后在`System Settings` - `Language Support`中选择`fcitx`，注销再登录，然后在键盘符号处点击`设置` - `+`添加sougoupinyin输入法即可。
## 若还无法正常使用
我的ubuntu14安装了搜狗输入法并设置后，仍然无法正常使用，[参考](http://www.linuxdiyf.com/linux/22075.html)，不过须把`im-switch`换成`im-config`才行。具体步骤如下：

第一，添加源：
```
sudo add-apt-repository ppa:fcitx-team/nightly
```
第二，更新系统
```
sudo apt-get update
```
第三，安装fcitx：
```
sudo apt-get install fcitx
```
第四，安装fcitx的配置工具：
```
sudo apt-get install fcitx-config-gtk
```
第五，安装fcitx的table-all软件包：
```
sudo apt-get install fcitx-table-all
```
第六，安装im-config:
因为我按照他原来的步骤操作时，最后一步提示我`im-switch`与安装冲突，要安装`im-config`，呵呵。
```
sudo apt-get install im-config
```
第七，用dpkg命令安装搜狗输入法资源包
```
sudo dpkg -i [资源包文件名.deb]
```
第八，设置语言选项
`System Settings` - `Language Support` - `fcitx`
第九，注销
第十，在fcitx配置中添加sougou pinyin。

## Small problems
### [Ubuntu-16-04-Pycharm无法切换搜狗输入法](http://sjq597.github.io/2016/08/29/Ubuntu-16-04-Pycharm%E6%97%A0%E6%B3%95%E5%88%87%E6%8D%A2%E6%90%9C%E7%8B%97%E8%BE%93%E5%85%A5%E6%B3%95/)
In Ubuntu 16.04, Sougou Input Method cannot be used in Cmaptools and Pycharm.
The solution is add the following 3 lines to the top of the start file such as `/usr/dev/pycharm-2016.2/bin/pycharm.sh`.
```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
```

Then we can type in Chinese character by Sougou Input Method in Pycharm. Solution for Cmaptools is similar. The only problem left is the small menu of alternative characters does not follow tightly behind the input curser any more. 


# Install CmapTools
Reference: [How to install a .bin file](http://askubuntu.com/questions/15093/how-can-i-install-a-bin-file)
```bash
chmod a+x name_of_file.bin
sudo ./name_of_file.bin
```
In order to get full access to Cmaps directory, I need to choose "where to install" as `/home/katherine/IHMC CmapTools` rather than `/root/IHMC CmapTools`. Then you can have the `My_Cmaps` file under directory `/home/katherine`.

Every time use `bash CmapTools` in the directory of file `CmapTools` to start it. 

# Install Pycharm
Reference: [Install a .tar.gz file](http://askubuntu.com/questions/25961/how-do-i-install-a-tar-gz-or-tar-bz2-file)
```bash
tar -zxvf /home/katherine/Documents/Software/pycharm-professional-2016.2.3.tar.gz -C /home/katherine/
cd /home/katherine/pycharm-2016.2.3/
```
Then:  
```bash
bash pycharm.sh
```
And the Installation process would start. This is also the way to start Pycharm after installation.

# Install Git
```bash
sudo apt-get install git 
sudo apt-get install git-core
```

# Get Access to Google by Firefox
## Install `shadowsocks` GUI
If you use Ubuntu like me, you can follow the code below.
```bash
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```
For other OS please see [here](https://github.com/shadowsocks/shadowsocks-qt5/wiki/安装指南).
You need to buy the service yourself, or you need a good boss like I have =.=

Then search "shadowsocks" in your computer and start the application. Configure the server and connect. 

## Install the Add-on `foxyproxy` for Firefox
Follow the steps in [http://sslite.top/136/](http://sslite.top/136/).
Make sure the shadowsocks is working when foxyproxy starts to work. 

# Other References
[How do I open, create, edit, and view a file in Linux?](http://www.computerhope.com/issues/ch000773.htm)


Almost all the problems and issues I met with are listed here. If you have other questions as well as answers, it's very appreciated to tell me. 


