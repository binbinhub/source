---
title: Install Softwares on Unbuntu 16.04.1
categories: 
tags: 
date: 
---

Installation of some softwares differs a lot between Ubuntu 14.04 and Ubuntu 16.04.

<--more!-->

# Open in Terminal

`sudo apt-get install nautilus-open-terminal` does not work. 
Got error message as below:
```bash
Package nautilus-open-terminal is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source
However the following packages replace it:
  gnome-terminal:i386 gnome-terminal

E: Package 'nautilus-open-terminal' has no installation candidate
```

`sudo apt-get update` and `sudo apt-get upgrade` made no progress. 

The following way of compiling from source code worked.
```bash
sudo apt-get install libgconf2-dev libnautilus-extension-dev intltool; 
wget https://launchpad.net/ubuntu/+archive/primary/+files/nautilus-open-terminal_0.20.orig.tar.xz; 
tar -xvf nautilus-open-terminal_0.20.orig.tar.xz; 
cd nautilus-open-terminal-0.20/; 
./configure; 
make; 
sudo make install
```

Reference:
[](http://askubuntu.com/questions/774605/how-can-nautilus-open-terminal-the-nautilus-context-menu-item-be-installed-on)

# Virtualenv

Since latest Python 3.5.2 is already in Ubuntu 16.04.1, it need not to be installed manually. 

## Add some other dependencies

```bash
sudo apt-get install libsqlite3-dev
sudo apt-get install sqlite3 # for the command-line client
sudo apt-get install bzip2 libbz2-dev

sudo apt-get install python3.5-dev
sudo apt-get install libncurses5-dev
```

## Install pip
```bash
sudo apt-get update
sudo apt-get install python3-pip
```
Reference:
[Install pip on Ubuntu 16.04](http://idroot.net/linux/install-pip-ubuntu-16-04/)

## Build virtual env of Python

```bash
sudo apt-get install build-essential
sudo pip3 install --upgrade pip
sudo pip3 install --upgrade virtualenv
virtualenv -p /usr/bin/python3.5 /home/katherine/venv/py35
source /home/katherine/venv/py35/bin/activate
```

## Configure 
```bash
sudo gedit ~/.bashrc
```
Add at the bottom of the text file, save and close:
```
# virtualenv
alias mypy35='source /home/katherine/venv/py35/bin/activate'
```

```bash
source ~/.bashrc
mypy35
```

# Install postgreSQL
```bash
sudo apt-get install postgresql-9.5 postgresql-contrib-9.5 postgresql-plpython3-9.5 postgresql-client-9.5 postgresql-common postgresql-server-dev-9.5
sudo apt-get install libpq-dev
```


# Install Useful Python Packages
**Activate your virtualenv**:
```bash
pip3 install pandas
pip3 install psycopg2
pip3 install sqlalchemy==1.1.1
pip3 install jupyter notebook
```

Install `psycopg2` after installing `postgreSQL`, or you would get error message:
```bash
    Error: pg_config executable not found.
    
    Please add the directory containing pg_config to the PATH
    or specify the full executable path with the option:
    
        python setup.py build_ext --pg-config /path/to/pg_config build ...
    
    or with the pg_config option in 'setup.cfg'.
    
    ----------------------------------------
Command "python setup.py egg_info" failed with error code 1 in /tmp/pip-build-wi220zjl/psycopg2/
```

# Install and Configure `postgreSQL` Extension `oracle_fdw`
```bash
git clone git://github.com/laurenz/oracle_fdw.git
cd oracle_fdw
make
sudo make install
```

```bash
sudo gedit /etc/ld.so.conf
```
Add `/home/katherine/cx_Oracle/instantclient_12_1` into the text file, save and close.
Then run:
```bash
sudo ldconfig
```

Then log on `postgreSQL` and run `create extension oracle_fdw;`

# Install Java
[How To Install Java on Ubuntu with Apt-Get](https://www.digitalocean.com/community/tutorials/how-to-install-java-on-ubuntu-with-apt-get)
