---
title: Generate Doc for Python Packages by sphinx
tags: 
  - python
  - sphinx
categories: 拿来主义|Copinism 
date: 2016-09-09 17:11:48
---
这篇是直接把领导整理的“如何利用sphinx自动为python package生成文档”搬过来的。
他是用jupyter notebook写的，放在公司内网的博客上。
我为了方便自己查阅，就硬搬过来了。最近还在找如何把jupyter notebook直接搬到Hexo blog上，然而还没有找到答案。

<!--more-->

## 安装`sphinx`

ubuntu下可以用
```bash
apt-get install python-sphinx
```

## 生成文档
### 建立文件夹

假设我们的文件结构是这样的
```
    some_path/
        /src
            /jfds
                __init__.py
                module1.py
                ...
```
其中jfds是python package对应的文件夹，如果要想在`some_path/`下创建名为`doc`的documentation文件夹，(和`src`并列)，那么可以在`some_path`打开`terminal`， 输入
```bash
    sphinx-apidoc -F -o docs src/
```
这样就自动生成了`doc`文件夹，包含了创建documentation所需的信息

### 添加package路径信息

如果jfds并未通过放入`site-packages`,加入`PYTHONPATH`等方式让`python`全局识别的话，如果修改`doc`文件夹中的`conf.py`, 添加以下行
```python
sys.path.append(os.path.join(os.path.dirname(os.path.abspath(__file__)),'../src'))
os.path.dirname(os.path.abspath(__file__))
```
的目的是找到`conf.py`的绝对路径，用`os.path.join`将它和`../src`合并后就找到了jfds这个package所在的绝对路径。

最后用
```python
sys.path.append
```
将jfds所在的路径加入，之后`sphinx`就能够找到`jfds` package

### 生成Documentation HTML

切换到`doc`目录，打开`terminal`，运行
```bash
make html
```
就能在`_build`文件夹找到自动生成的文档

## 文档编写方式

比较了几种风格，建议采用`google`的标准，这样可以让原始的docstring和生成的文档都有不错的可读性，[*样例见这里*](http://www.sphinx-doc.org/en/stable/ext/example_google.html)。

里面演示了如果编写`module`, `class`, `class method`, `function`等的文档。

这里节选一部分展示
```python
def module_level_function(param1, param2=None, *args, **kwargs):
    """This is an example of a module level function.

    Function parameters should be documented in the ``Args`` section. The name
    of each parameter is required. The type and description of each parameter
    is optional, but should be included if not obvious.

    Parameter types -- if given -- should be specified according to
    `PEP 484`_, though `PEP 484`_ conformance isn't required or enforced.

    If \*args or \*\*kwargs are accepted,
    they should be listed as ``*args`` and ``**kwargs``.

    The format for a parameter is::

        name (type): description
            The description may span multiple lines. Following
            lines should be indented. The "(type)" is optional.

            Multiple paragraphs are supported in parameter
            descriptions.

    Args:
        param1 (int): The first parameter.
        param2 (Optional[str]): The second parameter. Defaults to None.
            Second line of description should be indented.
        *args: Variable length argument list.
        **kwargs: Arbitrary keyword arguments.

    Returns:
        bool: True if successful, False otherwise.

        The return type is optional and may be specified at the beginning of
        the ``Returns`` section followed by a colon.

        The ``Returns`` section may span multiple lines and paragraphs.
        Following lines should be indented to match the first line.

        The ``Returns`` section supports any reStructuredText formatting,
        including literal blocks::

            {
                'param1': param1,
                'param2': param2
            }

    Raises:
        AttributeError: The ``Raises`` section is a list of all exceptions
            that are relevant to the interface.
        ValueError: If `param2` is equal to `param1`.


    .. _PEP 484:
       https://www.python.org/dev/peps/pep-0484/

    """
    if param1 == param2:
        raise ValueError('param1 may not be equal to param2')
    return True
```

