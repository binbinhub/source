---
title: Test Cases by pytest
date: 2016-09-09 17:40:05
tags:
  - python
  - pytest
categories: 拿来主义|Copinism
---
这篇同样是“拿来”的。
`pytest` will run all files in the current directory and its subdirectories of the form `test_.py` or `_test.py`. More generally, it follows standard test discovery rules.
<!--more-->
## python中的package和module
### module
`module`用于组织一系列相关的`function`和`class`,在文件系统中体现为一个`.py`文件。
### package
`package`用于组织一些列相关的`module`,在文件系统中体现为一个含有若干`.py`文件的文件夹。 不过要让`python`理解某个文件夹是`package`，需要在该文件夹中添加一个名为`__init__.py`的文件。`__init__.py`文件可以为空(这个文件有其他功能，这里不讨论)

文件夹可以存放其他文件夹，同理，package也可以包含其他的package。
## 文件系统中的文件组织形式

因此，`python`中的`package`和`module`，在文件系统中看应该是这样的
```
package1/  
    __init__.py  
    module1.py  
    module2.py  
    package2/  
        __init__.py  
        module1.py  
        module2.py  
package3/  
    __init__.py  
    module1.py  
    module2.py
```
## 如何让`python`找到自己编写的`package`？

如果我们自己写了一个`program.py`文件，希望它能够使用自己编写的`package`或者`module`，如何让`python`能够找到自己编写的`package`和`module`呢？

例如，在`program.py`中使用`import`导入所需的函数
```python
from package.module1 import some_function1
from module3 import some_function2
```
有以下几种方法

### 将`package`文件夹和`module`文件放在和`program.py`同级的文件夹下
```
someFolder/
    program.py
    package/
        __init__.py
        module1.py
        module2.py
        ...
    module3.py
    ...
```
### 将`package`文件夹和`module`文件放在`python`安装目录下的`site-packages`文件夹下

我自己`ubuntu`系统用`anaconda`发行版，`site-packages`的位置是`/home/usr/anaconda3/lib/python3.5/site-packages`

### 放在任意路径，将所在路径加入`PYTHONPATH`环境变量(这个方法对于`anaconda`似乎无效)
### 建议方法
这里建议的方式搭配`Git`,让`Git`统一管理`package`的版本演变，而每个项目单独`clone`一份所需的`package`。这样既能够保证有一份统一的代码，也不会由于向后兼容问题导致以前项目的代码无法运行。
## `pytest`的基本用法
### `pytest`测试用例代码的组织方式

由于团队使用代码时可能随时会对已有的代码做出修改，修改后要运行测试用例确保没有引入新的问题。因此将测试用例代码和功能代码放在一起会比较方便。建议的方式是
```
packageName/
    __init__.py
    module1.py
    module2.py
    ...
    test/
        __init__.py
        test_module1.py
        test_module2.py
        ...
```
注意`test`文件夹下也需要添加 `__init__.py`成为`package`，否则`test_module1.py`等测试代码使用如下的`relative import`的时候就会报错
```python
from ..module1 import func
```
如果要运行测试用例代码，可以在`packageName`所在的路径打开`terminal`，运行
```bash
py.test
```
然后`pytest`会自动遍历所有`test_`开头的`module`，然后执行其中`test`开头的`function`,以及`Test`开头的`class`中`test`开头的`method`并给出测试结果。

因此如果希望`module`, `function`, `class`, `class method`被`pytest`自动识别的话， 记得用`test`开头命名。(对于`class`, 使用`Test`)
## 测试用例的写法

例如写了下面一个功能函数`func`，功能是返回一个字符串`s`复制`n`次后进行拼接的结果，如果`s`不是字符串，那么抛出`TypeError`的`Exception`，并提示`s should be string!`。那么测试用例可以这么写。
```python
def test_value():
    assert func('ab',3) == 'ababab'
```
这里的关键是利用`assert`语句
```
assert [condition]
```
的功能是判断`condition`是否成立, 如果成立，什么也不发生，否则的话会`raise Exception`被`pytest`识别。

`condition`的内容一般是如果我们的函数正常生效，应该产生什么样的结果。

再上面的这个例子里,如果`func(s, n)`生效，那么`func('ab', 3)`应该等于`'ababab'`

为了检测`s`不为字符的时候，`func`是否正确的`raise Exception`，可以这么写。
```python
def test_error():
    with pytest.raises(TypeError) as error_info:
        func(1,3)
    
    assert 's should be string!' == str(error_info.value)
```
这里可以用`pytest.raises`构造一个`context-container`,然后构造一个场景，故意让被测函数触发`raise Exception`的场景。

`Exception`的信息会被`error_info`捕捉， 然后检查`str(error_info.value)`字符串中是否包含我们设想的`Exception`内容即可。


