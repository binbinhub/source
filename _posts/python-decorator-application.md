---
title: Python Decorator Application
tags:
  - python
  - decorator
categories: 程序媛|Coding
date: 2017-01-15 21:27:25
---
本篇文章简单介绍python装饰器的应用。
若多个不同的函数需要实现一个相同的功能，则可以考虑使用装饰器来实现，而不需要去修改每一个函数。
<!--more-->
# 情景描述1

有一个函数，存在一定概率会运行失败。


```python
def prob_fail():
    from random import randint
    if randint(0,100) < 20:
        print('Succeed')
    else:
        print('Fail')
        raise Exception
```


```python
prob_fail()
```

    Fail



    ---------------------------------------------------------------------------

    Exception                                 Traceback (most recent call last)

    <ipython-input-4-f3c46ba59b44> in <module>()
    ----> 1 prob_fail()
    

    <ipython-input-3-6bdb0df19324> in prob_fail()
          5     else:
          6         print('Fail')
    ----> 7         raise Exception
    

    Exception: 



```python
prob_fail()
```

    Succeed


# 希望能达到的效果是，在函数运行失败时自动重试。
## 不用装饰器也可实现


```python
while True:
    try:
        prob_fail()
        break
    except Exception as e:
        print('Retrying. ')
```

    Fail
    Retrying. 
    Fail
    Retrying. 
    Fail
    Retrying. 
    Fail
    Retrying. 
    Fail
    Retrying. 
    Fail
    Retrying. 
    Succeed


## 若多个函数都想实现此功能，用装饰器更方便
装饰器的特征：
* Closure 闭包函数
* 外层函数的参数只有一个，且是需要被装饰的函数


```python
def decorator(func):
    def inner():
        while True:
            try:
                return func()
            except Exception as e:
                print('Retrying. ')
    return inner

@decorator
def prob_fail():
    from random import randint
    if randint(0,100) < 20:
        print('Succeed')
    else:
        print('Fail')
        raise Exception
```

__之后只要直接调用函数本身即可。__


```python
prob_fail()
```

    Fail
    Retrying. 
    Fail
    Retrying. 
    Fail
    Retrying. 
    Fail
    Retrying. 
    Fail
    Retrying. 
    Fail
    Retrying. 
    Fail
    Retrying. 
    Fail
    Retrying. 
    Fail
    Retrying. 
    Fail
    Retrying. 
    Fail
    Retrying. 
    Succeed


## 若函数本身有参数，则需要在inner函数中补齐参数设置，以满足不同函数的调用需求


```python
def decorator(func):
    def inner(*args, **kargs):
        while True:
            try:
                return func(*args, **kargs)
            except Exception as e:
                print('Retrying. ')
    return inner
```

## 若想要在装饰器中增加别的参数以实现更多控制，则需要再包一层函数

此处设置了__最大重试次数(max_retry_n)__和__重试等待时间(retry_wait_arg)__的控制。  
若超过__最大重试次数__，则抛出MaxRetryNumError，这是Exception类的一个子类。


```python
class MaxRetryNumError(Exception):
    pass

def retry_control(max_retry_n, retry_wait_arg):
    from time import sleep
    def decorator(func):
        def inner(*args, **kargs):
            i = 0
            while True:
                try:
                    return func(*args, **kargs)

                except Exception as e:
                    wait_sec = retry_wait_arg*(i**2)
                    print('Wait {} seconds before retrying. '.format(wait_sec))
                    sleep(wait_sec)

                i += 1
                if i > max_retry_n:
                    raise MaxRetryNumError('Reach the Max Retry Number: {}! '.format(max_retry_n))
                print('Retry Time {}'.format(i))

        return inner
    return decorator
```


```python
@retry_control(3, 3)
def prob_fail():
    from random import randint
    if randint(0,100) < 20:
        print('Succeed')
    else:
        print('Fail')
        raise Exception
```


```python
prob_fail()
```

    Fail
    Wait 0 seconds before retrying. 
    Retry Time 1
    Fail
    Wait 3 seconds before retrying. 
    Retry Time 2
    Fail
    Wait 12 seconds before retrying. 
    Retry Time 3
    Fail
    Wait 27 seconds before retrying. 



    ---------------------------------------------------------------------------

    MaxRetryNumError                          Traceback (most recent call last)

    <ipython-input-18-f3c46ba59b44> in <module>()
    ----> 1 prob_fail()
    

    <ipython-input-15-b95d7b9d96bc> in inner(*args, **kargs)
         18                 i += 1
         19                 if i > max_retry_n:
    ---> 20                     raise MaxRetryNumError('Reach the Max Retry Number: {}! '.format(max_retry_n))
         21                 print('Retry Time {}'.format(i))
         22 


    MaxRetryNumError: Reach the Max Retry Number: 3! 

# 情景描述2（修改于2017-05-17）
现在有一堆函数，都是输入一个dataframe，输出一个dataframe。
现在为了运用既定的流程，并且保留dataframe的读取path，我希望所有的函数都能接收一个path和一个dataframe，输出也是path和dataframe。

## 原函数定义
用简单函数来做演示，`func1`的功能是给输入的数字加上`2`。
```python
def func1(x):
    return x+2
```
运行效果如下：
```python
func1(2)
```
    4

## 定义装饰器
```python
def decorator(func):
    def inner(s, x):
        return s, func(x)
    return inner
```

## 应用装饰器
装饰器定义好后，在每个原函数定义前加上`@decorator`。
```python
@decorator
def func1(x):
    return x+2
```
这样再调用`func1`时，`func1`的参数就从一个变成了两个，输出也相应地改变了。
```python
func1('x', 1)
```
    ('x', 3)

# 总结
装饰器`decorator`本身是一个函数，同时，它的输入参数必须是一个函数，就是需要被装饰的原函数`func`，它的输出`return`也是一个函数`inner`。`inner`负责调用原函数`func`，及进行相应的改造。

