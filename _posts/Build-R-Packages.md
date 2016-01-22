---
title: Build R Packages
date: 2016-01-21 16:32:29
categories: 程序媛|Coding
tags: [R, package]
---
After MM and I developed several R tools for our team, we began to look for more convenient way for the users. 
We were eager to build our own packages, but we didn't know how and didn't dig deep down. 
<!--more-->
Once I found our intern was using version control while working in RStudio. The **Project** concept came into our view. 
Surprisingly, **Projects** are just the basic concept for building R packages. 
In October 2015, I tried, failed and gave up. Now the needs for building R packages become more urgent so I start again. 

## Prerequisites
First of all, there are two main prerequisites for building R packages:
1. GNU software development tools including a C/C++ compiler; and
2. LaTeX for building R manuals and vignettes.

The following steps are suitable for **Windows** system. If you are using other OS, please look for help on [Package Development Prerequisites](https://support.rstudio.com/hc/en-us/articles/200486498).
### Rtools
The core software development utilities required for R package development can be obtained from the **Rtools** download on CRAN: https://cran.rstudio.com/bin/windows/Rtools/.
After downloading and installing the version of Rtools appropriate to the version of R you are using, you should also ensure that you've arranged your system PATH as recommended by Rtools (you can choose to do this automatically as part of Rtools installation if you like).
### MiKTeX
To build manuals and vignettes you'll also need to install the **MikTeX LaTeX** distribution for Windows which you can download from here: http://miktex.org/download.
If you have installed MiKTeX before like me, it is recommended that you [run the update wizard](http://miktex.org/howto/update-miktex) in order to get the latest updates.
Just go to **Startup** menu. Click **All Programs**. Find **MikTeX** then go to **Maintainence** and click **Update**. 
![](/images/20160121164233.png)
Then just click **Next**, **Next** and **Next**. If the first time going by "Selecting packages" it's unable for you to click on "Select All", don't worry, finish the update process and run all the steps once again. 

## Packages Needed Before Starting
### devtools
All `check` and `build` work is completed by package [devtools](https://cran.r-project.org/web/packages/devtools/index.html). Now find function `install_github()` and `install_local()` also quite useful. 
```R
install.packages("devtools")
```
### roxygen2
Package [roxygen2](https://cran.r-project.org/web/packages/roxygen2/index.html) is needed for generating R package documentations. 
```R
install.packages("roxygen2")
```
### packrat
[Packrat](https://cran.r-project.org/web/packages/packrat/index.html) is a Dependency Management System for Projects and their R Package Dependencies. 

Using packrat can make your R projects more:
+ **Isolated**: Installing a new or updated package for one project won’t break your other projects, and vice versa. That’s because packrat gives each project its own private package library.
+ **Portable**: Easily transport your projects from one computer to another, even across different platforms. Packrat makes it easy to install the packages your project depends on.
+ **Reproducible**: Packrat records the exact package versions you depend on, and ensures those exact versions are the ones that get installed wherever you go.

Packrat is now available on CRAN, so you can install it with:
```R
install.packages("packrat")
```
If you like to live on the bleeding edge, you can also install the development version of Packrat with:
```R
install.packages("devtools")
devtools::install_github("rstudio/packrat")
```

## Build A Package
### Project - New Project
First, we need to create a new project. On the top right corner of the RStudio window, you can find the "Project" button. 
![](/images/20160120194401.png)
Then select "New Directory". If you already have some R scripts but not a package yet, you can choose "Existing Directory". 
![](/images/20160121160957.png)
Then choose "R Package". *More exploration needed on other two choices.*
![](/images/20160121161110.png)
Name your package and tick "Use packrat with this project". 
![](/images/20160121161436.png)
After creating a brand new package, RStudio will automatically open a new R script file "hello.R". 
```R
# Hello, world!
#
# This is an example function named 'hello' 
# which prints 'Hello, world!'.
#
# You can learn more about package authoring with RStudio at:
#
#   http://r-pkgs.had.co.nz/
#
# Some useful keyboard shortcuts for package authoring:
#
#   Build and Reload Package:  'Ctrl + Shift + B'
#   Check Package:             'Ctrl + Shift + E'
#   Test Package:              'Ctrl + Shift + T'

hello <- function() {
  print("Hello, world!")
}
```
Under the package working directory, some files are automatically created. All the R scripts the package holds should be saved under the folder "R". 
![](/images/20160121162047.png)
You can edit the package DESCRIPTION. 
![](/images/20160121162919.png)
The DESCRIPTION file contains basic information about the package in the following format: 
> Package: pkgname
Version: 0.5-1
Date: 2015-01-01
Title: My First Collection of Functions
Authors@R: c(person("Joe", "Developer", role = c("aut", "cre"),
		     email = "Joe.Developer@some.domain.net"),
	      person("Pat", "Developer", role = "aut"),
	      person("A.", "User", role = "ctb",
		     email = "A.User@whereever.net"))
Author: Joe Developer [aut, cre],
  Pat Developer [aut],
  A. User [ctb]
Maintainer: Joe Developer <Joe.Developer@some.domain.net>
Depends: R (>= 3.1.0), nlme
Suggests: MASS
Description: A (one paragraph) description of what
  the package does and why it may be useful.
License: GPL (>= 2)
URL: https://www.r-project.org, http://www.another.url
BugReports: https://pkgname.bugtracker.url

Now just don't know where to see package DESCRIPTION. 

### Dependent Packages
You can install other necessary packages in the whole process of building your package. `packrat` can help record the package list and their version information. 
![](/images/20160122115721.png)
#### Snapshot
When you just install a packge, the "Version" and the "Source" column may have information while the "Packrat" column may be empty. The action of `packrat` saving information is called `snapshot`. 
Though I choose to "Automatically snapshot local changes" in Packrat "Project Options", I don't see the effect after waiting for a whole night ;P 
![](/images/20160122115826.png)
If so, you could manually snapshot by `packrat::snapshot()`. 
#### Remove Packages
If you find some packages unused, you can just use `remove.packages("unused package name")`. 
Or you can just let packrat to handle it. Click "Clean Unused Packages..." and `packrat` begins working. 
![](/images/20160122120100.png)   
#### Restore
If you change the package information by mistake, you can use `packrat::restore()` to restore the packrat library to the latest snapshot. 

### Coding
Now you can just define the functions you need. What's different is that you need to standardize the definition using `roxygen2` gramma. 
Here is an example which is enough for beginners. More information on [Writing Package Documentation](https://support.rstudio.com/hc/en-us/articles/200532317-Writing-Package-Documentation). 
```R
#' Arithmetic Mean
#' 
#' Generic function for calculating average value. 
#' @param x A numeric vector. 
#' @param ... further arguments passed to or from other methods.
#' @return The average value of the numeric vector, regardless of NAs. 
#' @seealso \code{\link{mean}} and \code{\link{weighted.mean})
#' @export 
average <- function(x, ...){
	y <- x[!is.na(x)]
	return(sum(y)/length(y))
}
```

#### Attention: 
+ `@param`:  All parameters should be mentioned and defined, or warnings would appear when checking. 
+ `@export`:  If `@export` is omitted, users are not able to call the funciton in Console. 
+ `::`:  If functions built from other packages are called inside your package, like `ggplot` instead of `ggplot2::ggplot`, even if "ggplot2" has already been installed and required, the Check session will warn you that there is no visible global function definition for `ggplot`. 
The warning message shows 
> Please use :: or requireNamespace() instead.
> See section '[Suggested packages](https://cran.r-project.org/doc/manuals/r-release/R-exts.html#Suggested-packages)' in the '[Writing R Extensions](https://cran.r-project.org/doc/manuals/r-release/R-exts.html)' manual.
However, I don't quite understand the "Suggested packages" section. 
I just recommend using `::` for every external functions you use since it works. 
*Still problems about calling other packages' functions, maybe [How R Searches and Finds Stuff](http://blog.obeautifulcode.com/R/How-R-Searches-And-Finds-Stuff/) can give the answer. 

### Build
Before asking RStudio to do the "Build" work, go to "Configure Build Tools" and tick "Generate documentation with Roxygen" and all inside choices.
This can make all your `#' ` sentences work and create *.Rd files under folder "man" automatically. You could also not use Roxygen and create *.Rd files by yourself. 
![](/images/20160122135403.png)
![](/images/20160122151841.png)
![](/images/20160122152145.png)
#### Check
RStudio will check if your package can work well and print out all the warnings, undocumented arguments, no visible global function definition, etc. 
After the check finishes, read the information carefully and review your code. 
#### Build & Reload
After there is no problems of the check, click "Build & Reload". Now it's time for you to `library(YourPackage)` and debug yourself. 
```R
hello()
average(1:5)
```
Try `?average` and see the documention built by Roxygen. 
![](/images/20160122160341.png)
#### More - Build Source Package & Build Binary Package
"Build Source Package" can output a *.tar.gz file; while "Build Binary Package" can output a *.zip file. 
Then it's very convenient to share your package. After the user recieve the file, they can go straight to use your packge! 
```R
devtools::install_local("~/Test_0.1.tar.gz")
library(test)
hello()
average(1:5)
```
---
## References

1. [Package Development Prerequisites](https://support.rstudio.com/hc/en-us/articles/200486498)
2. [Using Projects](https://support.rstudio.com/hc/en-us/articles/200526207-Using-Projects)
3. [Developing Packages with RStudio](https://support.rstudio.com/hc/en-us/articles/200486488-Developing-Packages-with-RStudio) 
4. [Packrat - Reproducible package management for R](http://rstudio.github.io/packrat/)
5. [Using Packrat with RStudio](http://rstudio.github.io/packrat/rstudio.html)
6. [Writing Package Documentation](https://support.rstudio.com/hc/en-us/articles/200532317-Writing-Package-Documentation)
7. [R Licenses](https://www.r-project.org/Licenses/)
8. [How R Searches and Finds Stuff](http://blog.obeautifulcode.com/R/How-R-Searches-And-Finds-Stuff/)