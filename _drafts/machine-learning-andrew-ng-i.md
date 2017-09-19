## What is Machine Learning?

Two definitions of Machine Learning are offered. 
Arthur Samuel described it as: 
> the field of study that gives computers the ability to learn without being explicitly programmed.

This is an older, informal definition.

Tom Mitchell provides a more modern definition: 
> A computer program is said to learn from __experience $E$__ with respect to some class of __tasks $T$__ and __performance measure $P$__, 
>
> if its performance at tasks in $T$, as measured by $P$, improves with experience $E$.



Example: playing checkers.

$E$ = the experience of playing many games of checkers
$T$ = the task of playing checkers.
$P$ = the probability that the program will win the next game.



什么是机器学习？

这里有两种定义。

一种老旧些非正式的说法来自Arthur Samuel，他说，机器学习是一个计算机学习的领域，它赋予计算机学习的能力，而不需要提前显式地编程。

另一种更现代的定义是Tom Mitchell给出的：如果一个电脑程序在任务$T$的表现的好坏$P$在经验$E$上提高了，就说这个电脑程序关于任务$T$和表现检测$P$对经验$E$进行了学习。

例如对于下棋，

$E$ - 下棋的经验（即历史数据）

$T$ - 下棋

$P$ - 程序赢得棋局的概率（即判断表现好坏与否的标准）



## Supervised Learning

In supervised learning, we are given a data set and already know what our correct output should look like, having the idea that there is a relationship between the input and the output.

Supervised learning problems are categorized into "regression" and "classification" problems. In a regression problem, we are trying to predict results within a continuous output, meaning that we are trying to map input variables to some continuous function. In a classification problem, we are instead trying to predict results in a discrete output. In other words, we are trying to map input variables into discrete categories.

**Example 1:**

Given data about the size of houses on the real estate market, try to predict their price. Price as a function of size is a continuous output, so this is a regression problem.

We could turn this example into a classification problem by instead making our output about whether the house "sells for more or less than the asking price." Here we are classifying the houses based on price into two discrete categories.

**Example 2**:

(a) Regression - Given a picture of a person, we have to predict their age on the basis of the given picture

(b) Classification - Given a patient with a tumor, we have to predict whether the tumor is malignant or benign.



监督式学习

监督式学习可分为回归问题和分类问题。

回归问题中，我们需要预测的变量是连续型的数据；而分类问题中，我们需要预测的变量是离散型的数据。



## Unsupervised Learning

Unsupervised learning allows us to approach problems with little or no idea what our results should look like. We can derive structure from data where we don't necessarily know the effect of the variables.

We can derive this structure by clustering the data based on relationships among the variables in the data.

With unsupervised learning there is no feedback based on the prediction results.

**Example:**

Clustering: Take a collection of 1,000,000 different genes, and find a way to automatically group these genes into groups that are somehow similar or related by different variables, such as lifespan, location, roles, and so on.

Non-clustering: The "Cocktail Party Algorithm", allows you to find structure in a chaotic environment. (i.e. identifying individual voices and music from a mesh of sounds at a [cocktail party](https://en.wikipedia.org/wiki/Cocktail_party_effect)).



非监督式学习

非监督式学习通常是聚类问题。