---
title: 抛弃Hexo又回归
categories: '看世界|World Eye'
tags: 
  - hexo
  - jupyter notebook
  - jupyter nbconvert
date: 2016-09-22 00:00:00
---

## 前因
现在的工作基本上都是借助jupyter notebook和Python内核来完成的。
做探索性的工作或者完成基本的分析报告都是如此，所以迫切地希望能够用jupyter notebook来直接完成一篇博文。
<!--more-->

## 经过
不过在网上搜索了良久，也没有找到[Hexo](https://hexo.io/)直接接受jupyter notebook作为博文文件类型的解决方案。

这时，蔡哥找到了一个工具；[pelican](http://blog.getpelican.com/)
它既可以接受jupyter notebook，同时也可以接受markdown文件作为博文，生成静态博客。

于是，我就抛弃Hexo了。把之前的文章也都迁移到了新的搭建在pelican上的博客里。

然而用了两天，也许是因为懒惰，觉得还是Hexo好啊！
Hexo的系统结构和配置方法，我不算精通，却也基本熟悉了。对pelican还是一头雾水呢。
其次，看Hexo的官方网站，可以感觉到它已经是一个非常成熟的工具了，而且支持许多外部的第三方服务，包括留言和分享等。（不知道pelican是否都支持。）

最最最重要的是，在pelican没有找到还算满意的主题啊！！！

## 解决方案
于是，Hexo，我又回来了，不要嫌弃我！哈哈哈哈！

### 格式转换工具[`jupyter nbconvert`](https://github.com/jupyter/nbconvert)
而支持jupyter notebook的问题，可以通过jupyter本身的工具[jupyter nbconvert](https://github.com/jupyter/nbconvert)来解决。

只需要一句简单的命令就可以把jupyter notebook转换成markdown文件。


```shell
jupyter nbconvert --to <output format> <input notebook> --output-dir <destination path>
jupyter nbconvert --to markdown mynotebook.ipynb --output-dir /home/katherine/Documents/hexo_blog/source/_posts/
```

`jupyter nbconvert`支持以下格式的文件之间的转换：
`custom`, `html`, `latex`, `markdown`, `notebook`, `pdf`, `python`, `rst`, `script`, `slides`

如果不用`--to`参数，则默认转换成html文件。

如果不用`--output-dir`参数，则默认转换成的文件保存在当前目录。

`<input notebook>`可以填写类似`my*.ipynb`的值，则`jupyter nbconvert`会自动转换当前路径下所有以my开头命名的notebook。

`jupyter nbconvert`生成markdown文件时，如果有图片等其他文件输出，会被保存到跟markdown文件同级的名为`<notebook_name>_files`的文件夹下。

### 转为Hexo可处理的形式

#### markdown文件

notebook的命名不要包含中文和空格，以免出现问题。

生成markdown文件后要进行检查。
Hexo支持的markdown文件，最开头的格式如下，且第一行不能为空行。nbconvert貌似帮我自动空了一行，需要手动删掉。建议在jupyter notebook中先不写这些信息，生成md文件后再补。

> \-------------------
>
> title: Hexo 背离又回归  
> categories: '看世界|World Eye'  
> tags:   
>
> \- tag1
>
> \- tag2
>
> date:  
>
> \----------------------



生成`new post`后会生成一个md文件，到时候用编辑好的md文件替换即可。

#### 图片等其他资料文件

生成的图片位置，要手动把图片添加到`/images`文件夹下，而文章里面的图片位置也要手动修改。
或者在站点配置文件中设置`post_asset_folder: true`，这样在每个post生成后，还会相对应地生成一个和md文件名相同的文件夹，把图片放进去后，把md文件中`![image label](/path/to/image_name.png)`改为**`{% asset_img <image_name.png> <image_label> %}`**即可。

**即要保证markdown的文件文件名要和同级路径中的asset_folder保持一致。**

好像有点复杂，不过可以完全倚赖Hexo的工具啊。感觉还是棒棒哒！

**20171120更新**  
在新的一篇blog中，我只是需要插入两张图片，然而用上述方法，只能正确显示第二张图片，第一张图片Hexo就把`{% asset_img <image_name.png> <image_label> %}`当做普通文本一样，而并没有正确显示图片。检查并尝试多次还是不行。

搜索到CodeFalling写的一个[小工具](https://github.com/CodeFalling/hexo-asset-image)，安装后还是把图片保存在与md文件同名的资源文件夹中，然后只要使用markdown原生的语法`![image_label](image_name.png)`就可以使图片正常显示了。既方便了图片的管理，也方便了图片的使用。安装方法很简单：
```bash
npm install hexo-asset-image --save
```

### 测试
这里，我们还要测试一下包含python代码的notebook转换成md文件后，还能否正常显示。


```python
%matplotlib inline
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt

sns.set(style="dark")
rs = np.random.RandomState(50)

# Set up the matplotlib figure
f, axes = plt.subplots(3, 3, figsize=(9, 9), sharex=True, sharey=True)

# Rotate the starting point around the cubehelix hue circle
for ax, s in zip(axes.flat, np.linspace(0, 3, 10)):

    # Create a cubehelix colormap to use with kdeplot
    cmap = sns.cubehelix_palette(start=s, light=1, as_cmap=True)

    # Generate and plot a random bivariate dataset
    x, y = rs.randn(2, 50)
    sns.kdeplot(x, y, cmap=cmap, shade=True, cut=5, ax=ax)
    ax.set(xlim=(-3, 3), ylim=(-3, 3))

f.tight_layout()
```

![测试seaborn等高线图的显示](hexo-leave-and-come-back_11_0.png)

