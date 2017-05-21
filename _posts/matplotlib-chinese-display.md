---
title: Ubuntu matplotlib 中文显示
categories: '程序媛|Coding'
tags:
  - ubuntu
  - python
  - matplotlib
  - jupyter notebook
date: 2016-10-10 00:00:00
---
本文介绍了好几种解决matplotlib中文显示问题的方法。

系统环境：
* Ubuntu 16.04 LTS
* Python3.5 virtual environment
* jupyter notebook
<!--more-->
## 1st, install some Chinese fonts

在ubuntu下执行以下命令即可完成安装。

`git clone https://github.com/tracyone/program_font && cd program_font && ./install.sh`

Source:

https://github.com/monkey0105/program_font



## 2nd, delete matplotlib cache list

Delete file `~/.cache/matplotlib/fontList.py3k.cache`

Use `Ctrl+h` to unhide the hidden files.

## 3rd, find available Chinese fonts both in matplotlib and ubuntu


```python
from matplotlib.font_manager import FontManager
import subprocess

fm = FontManager()
mat_fonts = set(f.name for f in fm.ttflist)

output = subprocess.check_output(
    'fc-list :lang=zh -f "%{family}\n"', shell=True)
output = output.decode('utf-8')
# print '*' * 10, '系统可用的中文字体', '*' * 10
# print output
zh_fonts = set(f.split(',', 1)[0] for f in output.split('\n'))
available = mat_fonts & zh_fonts

print('*' * 10, '可用的字体', '*' * 10)
for f in available:
    print(f)
```

    /home/katherine/venv/py35/lib/python3.5/site-packages/matplotlib/font_manager.py:273: UserWarning: Matplotlib is building the font cache using fc-list. This may take a moment.
      warnings.warn('Matplotlib is building the font cache using fc-list. This may take a moment.')
    /home/katherine/venv/py35/lib/python3.5/site-packages/matplotlib/font_manager.py:273: UserWarning: Matplotlib is building the font cache using fc-list. This may take a moment.
      warnings.warn('Matplotlib is building the font cache using fc-list. This may take a moment.')
    /home/katherine/venv/py35/lib/python3.5/site-packages/matplotlib/font_manager.py:273: UserWarning: Matplotlib is building the font cache using fc-list. This may take a moment.
      warnings.warn('Matplotlib is building the font cache using fc-list. This may take a moment.')
    /home/katherine/venv/py35/lib/python3.5/site-packages/matplotlib/font_manager.py:273: UserWarning: Matplotlib is building the font cache using fc-list. This may take a moment.
      warnings.warn('Matplotlib is building the font cache using fc-list. This may take a moment.')


    ********** 可用的字体 **********
    YouYuan
    KaiTi
    SimHei
    FangSong
    YaHei Consolas Hybrid
    Yahei Mono
    Microsoft YaHei
    LiSu


## 4th, find the directory of matplotlib font files


```python
import matplotlib
matplotlib.matplotlib_fname()
```




    '/home/katherine/venv/py35/lib/python3.5/site-packages/matplotlib/mpl-data/matplotlibrc'



If the output of the sentence above is  

`/home/katherine/anaconda3/lib/python3.5/site-packages/matplotlib/mpl-data/matplotlibrc`

Then the font file would be 

`/home/katherine/anaconda3/lib/python3.5/site-packages/matplotlib/mpl-data/fonts/`

__If there is no available font in the output of 3rd step, you need to copy one into the font file directory.__

#### I copied `/usr/share/fonts/MyFonts/YaHei.Consolas.1.11b.ttf` to `/home/katherine/anaconda3/lib/python3.5/site-packages/matplotlib/mpl-data/fonts/`. 

This font file stands for font named `YaHei Consolas Hybrid`. 

## 5th, edit file `matplotlibrc`

`font.family : {zh_family}, serif`

`font.serif : {zh_family}, ..., serif`

`font.sans-serif: {zh_family}, ...`

__Copy one available font name, e.g. `YaHei Consolas Hybrid`, to the place of `{zh_family}`.__

## 6th, check the result


```python
%matplotlib inline
```


```python
# -*- coding:utf-8 -*-
from pylab import *
x = [2,4,6,8,10]
y = [1224,838,632,626,624]
xlabel(u"text for x轴")
ylabel(u"text for y轴")
title(u"x轴和Y轴对应关系")
plot(x,y)
savefig('test')
show()
```

{% asset_img output_14_0.png %}

## Appendix
### Method 1 - failed


```python
import matplotlib.pyplot as plt
plt.rcParams['font.sans-serif'] = ['YaHei Consolas Hybrid']
#plt.rcParams['font.sans-serif'] = ['SimHei'] # 指定默认字体
plt.rcParams['axes.unicode_minus'] = False # 解决保存图像是负号'-'显示为方块的问题
  
t = arange(-5*pi, 5*pi, 0.01)  
y = sin(t)/t  
plt.plot(t, y) 
plt.title(u'这里写的是中文')  
plt.xlabel(u'X坐标')  
plt.ylabel(u'Y坐标')  
plt.show() 
```

{% asset_img output_16_0.png %}



```python
from pylab import mpl
mpl.rcParams['font.sans-serif'] = ['YaHei Consolas Hybrid'] # 指定默认字体
mpl.rcParams['axes.unicode_minus'] = False # 解决保存图像是负号'-'显示为方块的问题
  
t = arange(-5*pi, 5*pi, 0.01)  
y = sin(t)/t  
plt.plot(t, y)  
plt.title(u'这里写的是中文')  
plt.xlabel(u'X坐标')  
plt.ylabel(u'Y坐标')  
plt.show() 
```

{% asset_img output_17_0.png %}


### Method 2 - succeeded, but annoyed, and cannot do anything with axis tick label


```python
from pylab import *  
myfont = matplotlib.font_manager.FontProperties(fname='/usr/share/fonts/MyFonts/YaHei.Consolas.1.11b.ttf')  
mpl.rcParams['axes.unicode_minus'] = False  
t = arange(-5*pi, 5*pi, 0.01)  
y = sin(t)/t  
plt.plot(t, y)  
plt.title(u'这里写的是中文',fontproperties=myfont) #指定字体  
plt.xlabel(u'X坐标',fontproperties=myfont)  
plt.ylabel(u'Y坐标',fontproperties=myfont)  
plt.show() 
```

{% asset_img output_19_0.png %}


### Method in Seaborn
This method may succeed after succeed in matplotlib, and may fail if not. 


```python
import seaborn as sns
sns.set(font='YaHei Consolas Hybrid')
```


```python

```
