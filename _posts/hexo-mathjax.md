---
title: Hexo博客数学公式显示
categories: 程序媛|Coding
tags:
  - Hexo
  - MathJax
date: 2017-09-21 21:00:00
---
随着学习的深入，博客记录的笔记也少不了要碰到数学公式的展示问题。而Hexo好像并不支持markdown自带的数学公式语法，而是使用了Mathjax插件。本文介绍了__使用Next主题下的__Hexo博客启用Mathjax插件分方法和Mathjax的简单语法。
<!--more-->



# 启用Mathjax插件



## 安装

```bash
npm install hexo-math --save
```


## 配置



### 站点配置文件

在站点配置文件中加入以下内容：
```
math:
  engine: 'mathjax' # or 'katex'
  mathjax:
    src: custom_mathjax_source
    config:
      # MathJax config
  katex:
    css: custom_css_source
    js: custom_js_source # not used
    config:
      # KaTeX config
```



### 主题配置文件

编辑Next的主题配置文件， 将`mathjax`下的`enable`设定为`true`即可。`cdn`用于指定 MathJax的脚本地址，默认是MathJax官方提供的CDN地址。
```
mathjax:
  enable: true
  cdn: //cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML
```



# 基本语法对照表

* 嵌入行内的公式用`$`包裹，如：行内公式展示\$a = b + c\$，效果为：
  > 行内公式展示$a = b + c$

* 换行居中独立模块展示可以用`$$`包裹，如：模块展示\$\$a = b + c\$\$，效果为：
  > 模块展示$$a = b + c$$



## 基本

| 名称        | 语法                          | 显示                          | 备注               |
| --------- | --------------------------- | --------------------------- | ---------------- |
| 约等于       | `\approx`                   | $\approx$                   |                  |
| 不等于       | `\neq`                      | $\neq$                      |                  |
| 小于等于 大于等于 | `\leq` `\geq`               | $\leq$ $\geq$               |                  |
| 乘以 除以     | `\times` `\div`             | $\times$ $\div$             |                  |
| 加减        | `\pm`                       | $\pm$                       |                  |
| 上标        | `x^y`                       | $x^y$                       |                  |
| 上标        | `x^{y^z}`                   | $x^{y^z}$                   | `{}`扩住作为整体的`y^z` |
| 下标        | `x_n`                       | $x_n$                       |                  |
| 下标        | `x_{n_0}`                   | $x_{n_0}$                   | `{}`扩住作为整体的`n_0` |
| 开根号       | `\sqrt[n] x`                | $\sqrt[n] x$                |                  |
| 分数        | `\frac{1}{3}` 或 `1 \over 3` | $\frac{1}{3}$ 或 $1 \over 3$ |                  |
| 向量        | `\vec{a} \cdot \vec{b}`     | $\vec{a} \cdot \vec{b}$     |                  |
| 省略号       | `x_0, \ldots, x_n`          | $x_0, \ldots, x_n$          | 与_文本底线_对齐的省略号    |
| 省略号       | `x_0, \cdots, x_n`          | $x_0, \cdots, x_n$          | 与_文本中线_对齐的省略号    |



## 特殊运算符

| 名称    | 语法                                       | 显示                                       | 备注                     |
| ----- | ---------------------------------------- | ---------------------------------------- | ---------------------- |
| 累加    | `\sum_{i=0}^n x_i`                       | $\sum_{i=0}^n x_i$                       |                        |
| 累乘    | `\prod_{i=1}^n x_i`                      | $\prod_{i=1}^n x_i$                      |                        |
| 极限    | `\lim_{x \to +\infty} \frac{1}{x}`       | $\lim_{x \to +\infty} \frac{1}{x}$       |                        |
| 积分    | `\int_0^1 x^2 {\rm d}x`                  | $\int_0^1 x^2 {\rm d}x$                  |                        |
| 双重积分  | `\iint_1^\infty {1 \over x} {\rm d}x`    | $\iint_1^\infty {1 \over x} {\rm d}x$    |                        |
| 分情况   | `f(x)= \begin{cases} x, x>0 \\\ -x,x<0 \end{cases}` | $f(x)= \begin{cases} x, x>0 \\\ -x,x<0 \end{cases}$ | `\\\`代表换一行或者分一种情况      |
| 括号    | `() [] \{\} \lbrace \rbrace`             | $()$ $[]$ $\{\}$ $\lbrace \rbrace$       | 因为`{}`本身有标明整体的含义       |
| 大括号   | `\left( f(x) = \int_0^1 x^2 {\rm d}x \right)` | $\left( f(x) = \int_0^1 x^2 {\rm d}x \right)$ | 在括号前加上`\left`和`\right` |
| 因为所以  | `\because` `\therefore`                  | $\because$ $\therefore$                  |                        |
| 左右上下标 | `\sideset{^n_k}{^x_y}a`                  | $\sideset{^n_k}{^x_y}a$                  |                        |



## 希腊字母

| 名称       | 语法       | 显示       |
| -------- | -------- | -------- |
| $\alpha$ | `\alpha` | $\alpha$ |
| $\beta$  | `\beta`  | $\beta$  |
| $\gamma$ | `\gamma` | $\gamma$ |
| $\delta$ | `\delta` | $\delta$ |
| $\Delta$ | `\Delta` | $\Delta$ |
| $\theta$ | `\theta` | $\theta$ |
| $\Theta$ | `\Theta` | $\Theta$ |



## 表格

表格样式lcr表示居中，|加入一条竖线，\hline表示行间横线，列之间用&分隔，行之间用\分隔

```
$$\begin{array}{c|lcr}
n & \text{Left} & \text{Center} & \text{Right} \\\\
\hline
1 & 1.97 & 5 & 12 \\\\
2 & -11 & 19 & -80 \\\\
3 & 70 & 209 & 1+i \\\\
\end{array}$$
```

显示效果为：

$$\begin{array}{c|lcr}
n & \text{Left} & \text{Center} & \text{Right} \\\\
\hline
1 & 1.97 & 5 & 12 \\\\
2 & -11 & 19 & -80 \\\\
3 & 70 & 209 & 1+i \\\\
\end{array}$$



## 矩阵

```
$$\left[
\begin{matrix}
V_A \\\\
V_B \\\\
V_C \\\\
\end{matrix}
\right] =
\left[
\begin{matrix}
1 & 0 & L \\\\
-cosψ & sinψ & L \\\\
-cosψ & -sinψ & L
\end{matrix}
\right]
\left[
\begin{matrix}
V_x \\\\
V_y \\\\
W \\\\
\end{matrix}
\right] $$
```
显示效果为：

$$\left[
\begin{matrix}
V_A \\\\
V_B \\\\
V_C \\\\
\end{matrix}
\right] =
\left[
\begin{matrix}
1 & 0 & L \\\\
-cosψ & sinψ & L \\\\
-cosψ & -sinψ & L
\end{matrix}
\right]
\left[
\begin{matrix}
V_x \\\\
V_y \\\\
W \\\\
\end{matrix}
\right] $$



其余的用到了再补充吧，以上。




# References

* https://github.com/hexojs/hexo-math
* http://theme-next.iissnan.com/third-party-services.html#mathjax
* http://bennychen.me/mathjax.html
* http://daniellaah.github.io/2016/Mathmatical-Formula-within-Markdown.html
* http://stevenshi.me/2017/06/26/hexo-insert-formula/
