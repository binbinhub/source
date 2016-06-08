---
title: MySQL Database Backup and Restore
date: 2016-02-15 16:23:51
categories: 程序媛|Coding
tags: [MySQL, database, backup, restore]
---
MySQL的数据建了也有段日子了，但始终没有做备份，确实很不符合数据库的理论，倒是很符合我的懒癌风格。 
<!--more-->
# Using `mysqldump` in Command Prompt
在Google搜索"MySQL Backup Restore"出现最多的词应该就是`mysqldump`了，一开始以为是一个需要另外安装的软件，所以一直避开不看，结果绕了弯路。
`mysqldump`其实是完全复制创建数据库和数据表的过程，导出sql语句文件。这样，在恢复备份时，只需要重新跑一遍导出的sql语句即可。

## 优缺点
优点是快速复制了数据库和数据表的结构和内容；缺点是碰到如我现在碰到的庞大的表格（一开始一次性导入都十分困难，只能有R循环分批导入的表格），导出过程就如卡死了一般，非常缓慢，也不知道是否能够完全复制。

## 备份过程
1. 点击`Startup`，输入`cmd`，打开Windows Command Prompt。
2. 输入`cd C:\Program Files\MySQL\MySQL Server 5.6\bin`(具体以使用的版本和路径为准哟)。
3. `mysqldump`基本语法
	- 备份一个数据库中多个表： `mysqldump -u username -p dbname table1 table2 ...> BackupName.sql`
		+ *dbname*: 数据库的名称；
		+ *table n*: 需要备份的数据表的名称，为空则备份整个数据库；
		+ *BackupName.sql*: 备份文件的名称，文件名前面可以加上一个绝对路径。		
	- 备份多个数据库： `mysqldump -u username -p --databases dbname2 dbname2 ...> BackupName.sql`
	- 备份所有数据库： `mysqldump -u username -p --all-databases > BackupName.sql`
4. 输入`root`的password。

## 恢复过程
仍然在Windows Command Prompt中，把`>`之前最低一级单位的名称删除，然后把`>`换成`<`即可。
注意要在`C:\Program Files\MySQL\MySQL Server 5.6\bin`目录下。
- 恢复一个数据库中多个表： `mysqldump -u username -p dbname < BackupName.sql`	
- 恢复一个或多个数据库： `mysqldump -u username -p < BackupName.sql`

## 可能出现的问题
1. 我一开始把导出的目标文件夹命名为`MySQL DB Backup`，中间有空格，结果提示说`mysqldump: Couldn't find table: "DB"`，让我很是无语啊，于是就改成了`MySQL_Backup`。
2. 备份之前请一定检查一下目标文件夹的空间够不够，否则就会碰到我碰到的窘况。
> Backup failed: mysqldump: Got errno 28 on write
> Meaning： Insufficient disk space to store output.
> Solution： Free up some disk space. You might want to track down the reason you're low on disk space too.

# Copy `data` file from MySQL Server Path
原先二师兄好像也研究过这一块，然而今天在一篇博客中看到详解，让我茅塞顿开（虽然还来不及完全尝试╮(╯▽╰)╭）。

## 优缺点
据说是直接复制出来，要还原的时候直接粘贴在相同路径就欧儿了！

## 具体过程
这里说的`data`文件夹并不是`C:\Program Files\MySQL\MySQL Server 5.6\data`，而是**隐藏文件夹**： `C:\Users\All Users\MySQL\MySQL Server 5.6\data`。
1. 先设置`Folder and search options`中隐藏文件夹可见。
2. 找到文件夹`C:\Users\All Users\MySQL\MySQL Server 5.6\data`，打开可以看到之前创建的数据库。
3. 复制 & 粘贴。
4. 恢复时只要粘贴到此目录下即可。
5. 最好在备份好后仍然隐藏文件夹，以避免误删某些系统文件。

快去尝试一下吧！说你呢，二师兄！;p

---
# References
1. http://www.2cto.com/database/201406/312660.html
2. http://www.cnblogs.com/kissdodog/p/4174421.html
3. https://www.youtube.com/watch?v=AoZVLe2VqR4