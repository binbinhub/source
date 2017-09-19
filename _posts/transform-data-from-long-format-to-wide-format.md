---
title: Transform data from long format to wide format
tags: 
  - Pandas
  - SQLAlchemy
  - SQL
categories: 程序媛|Coding
date: 2017-09-19 16:20:00
---
在数据存储时，由于缺失等情况，为了节省存储空间，很多时候会采取long format，待到建模或分析等时候，wide format会使操作更便捷。  
于是，将long format的数据转换成wide format，就成了很常见的一种数据转换的场景。  
在pandas中运用unstack方法还是非常方便的，不过对于数据量很大的情况，pandas可能hold不住。  
本文记录了用pandas和sqlalchemy进行format转换的方法。  
<!--more-->


```python
import pandas as pd
import numpy as np
from sqlalchemy import create_engine, MetaData, Table, select, and_, func, case
```

# 数据观察

对于每个用户，有数据的维度可能是不同的。如果维度较多，而缺失情况较多的情况下，有时会采用long format的形式存储以降低存储空间。  
如下表所示，id列代表用户id，对于1号用户，prod有abcde五个维度，这五个维度都有数值num；对于2号用户，prod只有abcd四个维度，即维度e的值为空；同理，对于3号用户，维度b的值为空。


```python
df_long = pd.DataFrame(
    {
        'id': [1]*5+[2]*4+[3]*4,
        'prod': list('abcde')+list('abcd')+list('acde'),
        'num': np.random.randint(low=0, high=100, size=13)
    }
)
df_long[['id','prod','num']]
```

<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }
    
    .dataframe thead th {
        text-align: left;
    }
    
    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>prod</th>
      <th>num</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>a</td>
      <td>81</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>b</td>
      <td>39</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>c</td>
      <td>61</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>d</td>
      <td>90</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>e</td>
      <td>35</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2</td>
      <td>a</td>
      <td>83</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2</td>
      <td>b</td>
      <td>26</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2</td>
      <td>c</td>
      <td>76</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2</td>
      <td>d</td>
      <td>56</td>
    </tr>
    <tr>
      <th>9</th>
      <td>3</td>
      <td>a</td>
      <td>8</td>
    </tr>
    <tr>
      <th>10</th>
      <td>3</td>
      <td>c</td>
      <td>51</td>
    </tr>
    <tr>
      <th>11</th>
      <td>3</td>
      <td>d</td>
      <td>65</td>
    </tr>
    <tr>
      <th>12</th>
      <td>3</td>
      <td>e</td>
      <td>21</td>
    </tr>
  </tbody>
</table>




# pandas解决方案

在pandas中可以用groupby和agg方法进行聚合后，用unstack展开数据，一行代码即可搞定。


```python
df_long.groupby(['id','prod']).agg({'num':sum}).unstack()
```

<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }
    
    .dataframe thead th {
        text-align: left;
    }
    
    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="5" halign="left">num</th>
    </tr>
    <tr>
      <th>prod</th>
      <th>a</th>
      <th>b</th>
      <th>c</th>
      <th>d</th>
      <th>e</th>
    </tr>
    <tr>
      <th>id</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>81.0</td>
      <td>39.0</td>
      <td>61.0</td>
      <td>90.0</td>
      <td>35.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>83.0</td>
      <td>26.0</td>
      <td>76.0</td>
      <td>56.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>8.0</td>
      <td>NaN</td>
      <td>51.0</td>
      <td>65.0</td>
      <td>21.0</td>
    </tr>
  </tbody>
</table>




# sql解决方案

但在实际应用中，很多情况下是因为数据量过大才采用了long format的存储方式，这时候pandas可能无法胜任该任务，需要在sql或hive中解决。


```python
engine = create_engine('postgres://postgres:houzi0105@localhost:5432/bigdata')
engine.execute('CREATE SCHEMA IF NOT EXISTS test;')
```




    <sqlalchemy.engine.result.ResultProxy at 0x7f32758dcac8>




```python
df_long.to_sql('test', engine, schema='test', if_exists='replace', index=False)
```

## 原始数据


```python
metadata = MetaData(engine, schema='test')
tbl_test = Table('test', metadata, autoload=True, autoload_with=engine)
sql1 = select([tbl_test])

pd.read_sql(sql1, engine)
```

<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }
    
    .dataframe thead th {
        text-align: left;
    }
    
    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>num</th>
      <th>prod</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>81</td>
      <td>a</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>39</td>
      <td>b</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>61</td>
      <td>c</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>90</td>
      <td>d</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>35</td>
      <td>e</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2</td>
      <td>83</td>
      <td>a</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2</td>
      <td>26</td>
      <td>b</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2</td>
      <td>76</td>
      <td>c</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2</td>
      <td>56</td>
      <td>d</td>
    </tr>
    <tr>
      <th>9</th>
      <td>3</td>
      <td>8</td>
      <td>a</td>
    </tr>
    <tr>
      <th>10</th>
      <td>3</td>
      <td>51</td>
      <td>c</td>
    </tr>
    <tr>
      <th>11</th>
      <td>3</td>
      <td>65</td>
      <td>d</td>
    </tr>
    <tr>
      <th>12</th>
      <td>3</td>
      <td>21</td>
      <td>e</td>
    </tr>
  </tbody>
</table>




## 利用case when语法展开

这一步除了对case when语法的理解要求外，还需要掌握Python的list comprehensions和unpacking的技巧。


```python
sql2 = select(
    [
        tbl_test.c.id,
        *[
            case([(tbl_test.c.prod == prod, tbl_test.c.num)]).label(prod)
            for prod in pd.read_sql(select([tbl_test.c.prod]).distinct(), engine)['prod']
        ]
    ]
).alias('spread')
```

星号里面的list即为如下的case when语句：


```python
for x in [
    case([(tbl_test.c.prod == prod, tbl_test.c.num)]).label(prod)
    for prod in pd.read_sql(select([tbl_test.c.prod]).distinct(), engine)['prod']
]:
    print(x.compile(compile_kwargs={"literal_binds": True}))
```

    CASE WHEN (test.test.prod = 'c') THEN test.test.num END
    CASE WHEN (test.test.prod = 'b') THEN test.test.num END
    CASE WHEN (test.test.prod = 'a') THEN test.test.num END
    CASE WHEN (test.test.prod = 'e') THEN test.test.num END
    CASE WHEN (test.test.prod = 'd') THEN test.test.num END


得到的结果如下。可以看到，数据行数没有发生变化，但是列的维度得到了扩展，列名与prod列中的维度名一致，对应的值在对应的位置，若对应位置无对应值则为空。


```python
pd.read_sql(sql2, engine)
```

<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }
    
    .dataframe thead th {
        text-align: left;
    }
    
    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>c</th>
      <th>b</th>
      <th>a</th>
      <th>e</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>81.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>NaN</td>
      <td>39.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>61.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>90.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>35.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>83.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2</td>
      <td>NaN</td>
      <td>26.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2</td>
      <td>76.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>56.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>10</th>
      <td>3</td>
      <td>51.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>11</th>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>65.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>3</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>21.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>




## 对扩展数据进行聚合

按id分组，对每一个维度的数据取max，max函数会自动舍弃空值，保留原始数值。


```python
sql3 = select(
    [
        sql2.c.id,
        *[func.max(sql2.c[col]).label(col) for col in sql2.c.keys() if col != 'id']
    ]
).group_by(sql2.c.id)
```


```python
pd.read_sql(sql3, engine)
```

<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }
    
    .dataframe thead th {
        text-align: left;
    }
    
    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>c</th>
      <th>b</th>
      <th>a</th>
      <th>e</th>
      <th>d</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>61</td>
      <td>39.0</td>
      <td>81</td>
      <td>35.0</td>
      <td>90</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>51</td>
      <td>NaN</td>
      <td>8</td>
      <td>21.0</td>
      <td>65</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>76</td>
      <td>26.0</td>
      <td>83</td>
      <td>NaN</td>
      <td>56</td>
    </tr>
  </tbody>
</table>




## 查看SQL语句

这样，只需简单两段SQL语句，就完成了数据从long format到wide format的转换。


```python
print(sql2.compile(compile_kwargs={'literal_binds': True}))
```

    SELECT test.test.id, CASE WHEN (test.test.prod = 'c') THEN test.test.num END AS c, CASE WHEN (test.test.prod = 'b') THEN test.test.num END AS b, CASE WHEN (test.test.prod = 'a') THEN test.test.num END AS a, CASE WHEN (test.test.prod = 'e') THEN test.test.num END AS e, CASE WHEN (test.test.prod = 'd') THEN test.test.num END AS d 
    FROM test.test



```python
print(sql3.compile(compile_kwargs={'literal_binds': True}))
```

    SELECT spread.id, max(spread.c) AS c, max(spread.b) AS b, max(spread.a) AS a, max(spread.e) AS e, max(spread.d) AS d 
    FROM (SELECT test.test.id AS id, CASE WHEN (test.test.prod = 'c') THEN test.test.num END AS c, CASE WHEN (test.test.prod = 'b') THEN test.test.num END AS b, CASE WHEN (test.test.prod = 'a') THEN test.test.num END AS a, CASE WHEN (test.test.prod = 'e') THEN test.test.num END AS e, CASE WHEN (test.test.prod = 'd') THEN test.test.num END AS d 
    FROM test.test) AS spread GROUP BY spread.id


感谢师父的提示和帮助！
