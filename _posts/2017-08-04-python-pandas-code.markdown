---
layout:     post
title:      "Python pandas 学习笔记"
date:       2017-08-04 14:10:00
author:     "Daniel"
header-img: "img/post-bg-python.jpg"
tags:
    - Python
    - Pandas

---


#### 手动创建Serial
``` python
s = pd.Series([4, 7, -5, 3], index=["d", "b", "a", "c"])
s[["c", "a", "d"]]
c    3
a   -5
d    4
dtype: int64

sdata = {"Ohio": 35000, "Texas": 71000, "Oregon": 16000, "Utah": 5000}
s1 = pd.Series(sdata)

# 生成连续日期作为DatetimeIndex

dates = pd.date_range("1/1/2000", periods=7)
ts = pd.Series(np.arange(7), index=dates)
```

#### 手动创建DataFrame
``` python
data = {"state": ["Ohio", "Ohio", "Ohio", "Nevada", "Nevada"],
        "year": [2000, 2001, 2002, 2001, 2002],
        "pop": [1.5, 1.7, 3.6, 2.4, 2.9]}
df = pd.DataFrame(data, columns=["year", "state", "pop"],
                   index=["one", "two", "three", "four", "five"])

       year   state  pop
one    2000    Ohio  1.5
two    2001    Ohio  1.7
three  2002    Ohio  3.6
four   2001  Nevada  2.4
five   2002  Nevada  2.9

# dataframe to ndarray

df.values
df.as_matrix()
np.array(df)
array([[2000, "Ohio", 1.5],
       [2001, "Ohio", 1.7],
       [2002, "Ohio", 3.6],
       [2001, "Nevada", 2.4],
       [2002, "Nevada", 2.9]], dtype=object)

ser = pd.Series(data)
state    [Ohio, Ohio, Ohio, Nevada, Nevada]
year         [2000, 2001, 2002, 2001, 2002]
pop               [1.5, 1.7, 3.6, 2.4, 2.9]

# series to ndarray

ser.values
array([list(["Ohio", "Ohio", "Ohio", "Nevada", "Nevada"]),
       list([2000, 2001, 2002, 2001, 2002]),
       list([1.5, 1.7, 3.6, 2.4, 2.9])], dtype=object)

df.as_matrix(["pop"])
array([[1.5],
       [1.7],
       [3.6],
       [2.4],
       [2.9]])


import csv
lines = list(csv.reader(open("country.csv")))
header, values = lines[0], lines[1:]
values
	[["2000", "Ohio", "1.5"],
	 ["2001", "Ohio", "1.7"],
	 ["2002", "Ohio", "3.6"],
	 ["2001", "Nevada", "2.4"],
	 ["2002", "Nevada", "2.9"]]
list(zip(*values))
	[("2000", "2001", "2002", "2001", "2002"),
	 ("Ohio", "Ohio", "Ohio", "Nevada", "Nevada"),
	 ("1.5", "1.7", "3.6", "2.4", "2.9")]
data_dict = {h: v for h, v in zip(header, zip(*values))}
	{"pop": ("1.5", "1.7", "3.6", "2.4", "2.9"),
	 "state": ("Ohio", "Ohio", "Ohio", "Nevada", "Nevada"),
	 "year": ("2000", "2001", "2002", "2001", "2002")}
```

#### 导入\导出csv文件
``` python
df = pd.read_csv("pizza.csv", parse_dates=["date"], infer_datetime_format=True, encoding="utf-8")
0 order_number       date         size  price coupon
0     PZZA0001 2016-08-21        Small  12.99    NaN
1     PZZA0000        NaT        Large  14.50     No
2     PZZA0001 2016-09-27  Extra Large  19.99     No
3     PZZA0002 2016-09-28          NaN    NaN    Yes
4     PZZA0003 2016-09-29  Extra Large    NaN     No
df.to_csv("apple.csv", sep=",", encoding="utf-8", index=False, columns=["size", "order_number", "price"])
# 或者指定要读取列

df = pd.read_csv("pizza.csv", usecols=["foo", "bar"])
# 分块读取

chunksize = 500
chunks = []
for chunk in pd.read_csv("pizza.csv", chunksize=chunksize):
    # Do stuff...
    chunks.append(chunk)

df = pd.concat(chunks, axis=0)

# 移除unnamed列

df.loc[:, ~df.columns.str.contains("^Unnamed")]
pd.read_csv("data.csv", index_col=0)


df_test = pd.read_csv("http://mlr.cs.umass.edu/ml/machine-learning-databases/adult/adult.test", names=COLUMNS, skipinitialspace=True, skiprows=1)
df_test = pd.read_csv(test_data, sep=",", header=None, names=COLUMNS, engine="python", skipinitialspace=True, skiprows=1, skipfooter=1)

df2 = pd.read_csv(file, sep="\t", skiprows=10000, nrows=100)
dfn = pd.read_csv(file, sep="\t", skiprows=(n-1)*10000, nrows=100) # skiprows 也可以为list
```

#### 探索DataFrame中的数据
``` python
# first five rows

df.head()   
df.iloc[:5]
# last five rows  

df.tail()     
# random sample of rows  

df.sample(5)  
# number of rows/columns in a tuple  

df.shape     
# calculates measures of central tendency 

df.describe()  
# memory footprint and datatypes 

df.info()  
df.info(memory_usage="deep")   
# list unique values in a DataFrame column  

pd.unique(df.order_number.ravel())
# 某列unique values

df[c].unique()
# 某列unique values的个数

df[c].nunique()
```

#### 重命名列名
``` python
>>> df = df.rename(columns={"old1":"new1", "old2":"new2"})
# 列名小写

df.columns = map(str.lower, df.columns)
```

#### 插入\删除一列
``` python
double_price = df.price * 2
# 第一个参数为列的位置

df.insert(4, "d_price", double_price)
  order_number       date         size  d_price  price coupon
0     PZZA0001 2016-08-21        Small    25.98  12.99    NaN
1     PZZA0000        NaT        Large    29.00  14.50     No
2     PZZA0001 2016-09-27  Extra Large    39.98  19.99     No
3     PZZA0002 2016-09-28          NaN      NaN    NaN    Yes
4     PZZA0003 2016-09-29  Extra Large      NaN    NaN     No
# 或者根据条件添加

df["profitable"] = np.where(df["price"]>=15.00, True, False)
# 删除某一列

del df["profitable"]
# axis=0 for rows and axis=1 for columns

df.drop("profitable", axis=1, inplace=True)
```

#### 插入\删除一行
``` python
# 最后一行插入

df.loc[df.shape[0]] = ["PZZA0001", "2016-08-21", "Small", 25.98, 12.99, NaN]
# 删除第k行(index)

df = df.drop("k")
df.drop(df.index[[1,3]], inplace=True)
```

#### 追加一行
``` python
df.loc[df.shape[0]]={"order_number":"PZZA0001", "date":2, "size":"Small", "d_price":33.22}
df.loc[df.shape[0]]=["PZZA0001", 2, "Small", 33.22]
# 将Serial转化为dict

df.loc[df.shape[0]]=dict(df.loc[1])
```

#### 操作某一列
``` python
df["order_number"].str[0:4]
0    PZZA
1    PZZA
2    PZZA
3    PZZA
4    PZZA
Name: order_number, dtype: object
df["order_number"].str.lower()
0    pzza0001
1    pzza0000
2    pzza0001
3    pzza0002
4    pzza0003
Name: order_number, dtype: object
df["order_number"].str.len()
# 不同类型的个数

df["order_number"].value_counts()

# 根据其他列设置column的值，布尔索引

df.loc[(df.order_number == "PZZA0001") & (df.coupon == "No"), ["type"]] = "Bigger"
df
  order_number                 date         type  price coupon
0     PZZA0001  1471737600000000000        Small  12.99    NaN
1     PZZA0000 -9223372036854775808        Large   14.5     No
2     PZZA0001  1474934400000000000       Bigger    abc     No
3     PZZA0002  1475020800000000000          NaN      0    Yes
4     PZZA0003  1475107200000000000  Extra Large      0     No
# 三目运算

df["type"] = np.where((df["order_number"]=="PZZA0001") & (df["coupon"] == "No"), "Bigger", df["type"])
df["type"].where((df["order_number"]=="PZZA0001") & (df["coupon"] == "No"), "Bigger", inplace=True)


# 根据列值选择行

df.loc[df["type"] == "Small"]
df.loc[df["type"].isin(["Small", "Bigger"])]
df.loc[~df["type"].isin(["Small", "Bigger"])]

# 合并两个column生成新的column

df["order_number"] = df["date"].map(str) + df["type"].map(str)
df["order_number"] = np.where(pd.isnull(df["date"]), 0, df["date"]) + df["type"]
# 分割为两个column

df["date"], df["type"] = zip(*df["order_number"].apply(lambda x: x.split(":", 1)))
# 取值映射

df["coupon"].map({"Yes": True, "No": False})
df["type"].replace("Small", "Small-1")
```

#### 选择特定的单元格值
``` python
df.ix[2, "date"]
Timestamp("2016-09-27 00:00:00")
df.date.ix[2]
Timestamp("2016-09-27 00:00:00")
# set

df.set_value("index", "column1", 10)
# get  loc第一个参数对应的是index，iloc第一个参数对应的是相对位置

df.iloc[0, 0]
df.loc[0,"column1"]

df.ix[0,"column1"]
df.ix[:2,:2]
# 最后一行column1对应的cell，df1.iloc[[-1]]对应最后一行的Serial，不能写成df1.iloc[-1]，传入的应该为index

df1.iloc[[-1]].iloc[0]["column1"]
# 选择数据在行[3, 4, 8]和列["column1", "column2"]

df.loc[df.index[[3, 4, 8]], ["column1", "column2"]]
```

#### DataFrame for循环
``` python
for index, row in df.iterrows():
    print index, row["price"]

# 更快的遍历方式，使用tuple

for row in df.itertuples(index=False):
    print(row)
```

#### 过滤DataFrame
``` python
filtered_data = df[(df.order_number == "PZZA0001") & (df.price > 13)]
0 order_number       date         size  d_price  price coupon
2     PZZA0001 2016-09-27  Extra Large    39.98  19.99     No
# 过滤某列包含特定值

order_list = ["PZZA0001", "PZZA0000"]
df[df.order_number.isin(order_list)]
  order_number       date         size  d_price  price coupon 
0     PZZA0001 2016-08-21        Small    25.98  12.99    NaN 
1     PZZA0000        NaT        Large    29.00   14.5     No 
2     PZZA0001 2016-09-27  Extra Large    39.98    abc     No 
# 取反

df[~df.order_number.isin(order_list)]
# 选择order_number不为nan的行

df[~df["order_number"].isnull()]
# 选择price在[12, 14)之间的行

df[df["price"].between(12, 14)]
```

#### 排序
``` python
df.sort_values(by=["date", "price"], ascending=[1, 0], inplace=False)
  order_number       date         size  d_price  price coupon
1     PZZA0000        NaT        Large    29.00  14.50     No
0     PZZA0001 2016-08-21        Small    25.98  12.99    NaN
2     PZZA0001 2016-09-27  Extra Large    39.98  19.99     No
3     PZZA0002 2016-09-28          NaN      NaN    NaN    Yes
4     PZZA0003 2016-09-29  Extra Large      NaN    NaN     No
```

#### 某一列的每一行施加函数
``` python
df = pd.DataFrame(
	{
       "sp" : ["MM1", "MM1", "MM1", "MM2", "MM2", "MM2"],
       "mt" : ["S1", "S1", "S3", "S3", "S4", "S4"], 
       "value" : [3,2,5,8,10,1]     
    }
)

# Series.map()

# 使用字典进行映射

df["sp"] = df["sp"].map({"MM1":1, "MM2":2})
​
# 使用函数

def sp_map(x):
    return 1 if x == "MM1" else 0

df["sp"] = df["sp"].map(sp_map)
​
def apply_val(x, bias):
    return x + bias
​
# 以元组的方式传入额外的参数

df["value"] = df["value"].apply(apply_val, args=(-3,))

df["new_val"] = df.apply(lambda x: str(x["value"]) + x["mt"], axis=1)

```


#### 均值方差
``` python
# axis=0 列 axis=1 行

df.mean(axis=1)
df.std(axis=0)
# 减去每行的均值

df.sub(df.mean(axis=1), axis=0)
# 每列和的最小值对应的列

df.sum().idxmin()
```

#### 转化为Numpy Array
``` python
df.values
# 保留表格的表示

df.as_matrix
```

#### 改变列的数据类型
``` python
>>> df["date"] = df["date"].astype(np.int64)
>>> df
  order_number                 date         type  price coupon
0     PZZA0001  1471737600000000000        Small  12.99    NaN
1     PZZA0000 -9223372036854775808        Large   14.5     No
2     PZZA0001  1474934400000000000  Extra Large    abc     No
3     PZZA0002  1475020800000000000          NaN      0    Yes
4     PZZA0003  1475107200000000000  Extra Large      0     No
```

#### category 类型
``` python
df_data[sparse_columns] = df_data[sparse_columns].apply(pd.Series.astype, dtype="category")
df_data[sparse_columns] = df_data[sparse_columns].apply(pd.Categorical, ordered=True)

category_mapping = {feat: list(df_data[feat].cat.categories) for feat in category_features}
for feat in category_features:
    df_data[feat] = np.array(df_data[feat].cat.codes)
```

#### 拼接DataFrame/Serial
``` python
# axis=0 vertical axis=1 horizontal

pd.concat([df_1, df_2], axis=0, ignore_index=True)
pd.concat([df_1, df_2], axis=1)

s1 = pd.Series([0, 1], index=["a", "b"])
s2 = pd.Series([2, 3, 4], index=["c", "d", "e"])
s3 = pd.Series([5, 6], index=["f", "g"])
pd.concat([s1, s2, s3])
	a    0
	b    1
	c    2
	d    3
	e    4
	f    5
	g    6
	dtype: int64

pd.concat([s1, s2, s3], axis=1, keys=["one", "two", "three"])
		one	two	three
	a	0.0	NaN	NaN
	b	1.0	NaN	NaN
	c	NaN	2.0	NaN
	d	NaN	3.0	NaN
	e	NaN	4.0	NaN
	f	NaN	NaN	5.0
	g	NaN	NaN	6.0

result = pd.concat([s1, s2, s3], keys=["one", "two", "three"])
result.unstack()
		a	b	c	d	e	f	g
	one	0.0	1.0	NaN	NaN	NaN	NaN	NaN
	two	NaN	NaN	2.0	3.0	4.0	NaN	NaN
	three	NaN	NaN	NaN	NaN	NaN	5.0	6.0
```

#### DataFrame join操作
``` python
# how表示join的方式，取值：inner, outer, left, right 
# on表示join条件字段

merged_df = df_1.merge(df_2, how="left", on="order_id")
```

#### 统计空值个数
``` python
# 统计所有的空值

df.isnull().sum().sum()                        
9               
# 统计每一列的空值个数  

df.isnull().sum()
order_number    0
date            1
size            1
d_price         2
price           2
coupon          1
taxes           2
dtype: int64            
```

#### 填补空值
``` python
# 检查每一列是否有空值

def num_missing(x):
  return sum(x.isnull())

print(df.apply(num_missing, axis=0))


df.fillna({"price":0.0, "d_price":0.0}, inplace=True)
# 用众数填充

from scipy.stats import mode
# 众数是数组，因为可能会出现多个高频值

df["price"].fillna(mode(df["price"]).mode[0], inplace=True)

# 删除所有包含空值的行

df = df.dropna(axis=0)
# 删除空值个数大于7的行

df = df.dropna(thresh=df.shape[1] - 7)
# 删除price列和d_price都为nan的行，与之相反的是how="any"

df.dropna(subset=["price", "d_price"], how="all")
# NaN转化为None，写入db之前非常有用 

df = df.where((pd.notnull(df)), None)
# 对DataFrame中的每个单元格执行指定函数的操作，trim字符串空白，空值改为None

df = df.applymap(lambda x: str(x).strip() if len(str(x).strip()) else None)

# 删除全行为0的行 

df = pd.DataFrame({"a":[0,0,1,1], "b":[0,1,0,1]})       
df                                                      
   a  b                                                     
0  0  0                                                     
1  0  1                                                     
2  1  0                                                     
3  1  1
# 删除全行为0的行 

df.loc[~(df==0).all(axis=1)]                            
   a  b                                                     
1  0  1                                                     
2  1  0                                                     
3  1  1                                                     
df.loc[(df!=0).any(axis=1)]                             
   a  b                                                     
1  0  1                                                     
2  1  0                                                     
3  1  1  
# 删除有为0的行     

df.loc[(df!=0).all(axis=1)]                              
   a  b                                                     
3  1  1    
```

#### groupby某一列
``` python
>>> df.groupby("order_number")["price"].apply(lambda x: np.mean(x))
order_number
PZZA0000    14.50
PZZA0001    16.49
PZZA0002     0.00
PZZA0003     0.00
Name: price, dtype: float64
# 对分组进行迭代

for (k1, k2), group in df.groupby(["order_number", "date"]):
    print((k1, k2))
    print(group)

# 列表推倒和df分组

result_list = [extract_value(group, enter, leave) for uid, group in df.groupby("uid")]
result = pd.concat(list(filter(lambda x: len(x) > 0, result_list)))
```

#### 创建bin
``` python
bins = [-5, 5, 15, 20]
names = ["Cheap", "Normal", "Expensive"]
df["price_point"] = pd.cut(df.price, bins, labels=names)
  order_number       date         size  d_price  price coupon price_point
0     PZZA0001 2016-08-21        Small    25.98  12.99    NaN      Normal
1     PZZA0000        NaT        Large    29.00  14.50     No      Normal
2     PZZA0001 2016-09-27  Extra Large    39.98  19.99     No   Expensive
3     PZZA0002 2016-09-28          NaN     0.00   0.00    Yes       Cheap
4     PZZA0003 2016-09-29  Extra Large     0.00   0.00     No       Cheap
```

#### 移除非数字的值
``` python
# 如price为float64，有一个cell中的值为'abc'，经过处理变为nan

>>> df = df.astype(str).convert_objects(convert_numeric=True)
```

#### 重复记录
``` python
# keep=False，保留重复记录

>>> df[df.duplicated(["order_number", "type"], keep=False)]
>>> df.drop_duplicates(["order_number", "type"], take_last=True)
```

#### 获取某列分组最大值所在的行
``` python
df = pd.DataFrame({
               "sp" : ["MM1", "MM1", "MM1", "MM2", "MM2", "MM2"],
               "mt" : ["S1", "S1", "S3", "S3", "S4", "S4"], 
               "value" : [3,2,5,8,10,1]     
                })  
    Sp  Mt Value  count                                                               
0  MM1  S1     a      3                                          
1  MM1  S1     n      2                                             
2  MM1  S3    cb      5                                                            
3  MM2  S3    mk      8                                             
4  MM2  S4    bg     10                                           
5  MM2  S4   dgd      1                                             
6  MM4  S2    rd      2                                           
7  MM4  S2    cb      2                                             
8  MM4  S2   uyi      7                                                                  
df.groupby(["Mt"], sort=False)["count"].max()                                        
Mt              
S1     3                                                                              
S3     8                                                                           
S4    10                                                                         
S2     7                                                                                 
Name: count, dtype: int64                                                                
idx = df.groupby(["Mt"], sort=False)["count"].transform(max) == df["count"]          
df[idx]                                                                              
    Sp  Mt Value  count                                                                  
0  MM1  S1     a      3                                                            
3  MM2  S3    mk      8                                            
4  MM2  S4    bg     10                                             
8  MM4  S2   uyi      7                                                                  
# 或者增加一列

df["count_max"] = df.groupby(["Mt"])["count"].transform(max)                   
df                                                                                   
    Sp  Mt Value  count  count_max                                                 
0  MM1  S1     a      3          3                     
1  MM1  S1     n      2          3                    
2  MM1  S3    cb      5          8                     
3  MM2  S3    mk      8          8                      
4  MM2  S4    bg     10         10                                                 
5  MM2  S4   dgd      1         10                     
6  MM4  S2    rd      2          7                     
7  MM4  S2    cb      2          7                     
8  MM4  S2   uyi      7          7                                                         
df[df["count"]==df["count_max"]]                                                     
    Sp  Mt Value  count  count_max                           
0  MM1  S1     a      3          3                                                 
3  MM2  S3    mk      8          8                     
4  MM2  S4    bg     10         10                                                 
8  MM4  S2   uyi      7          7                 
```

#### 分组的其他操作
``` python
>>> df = pd.DataFrame({"grps": list("aaabbcaabcccbbc"),                    
                       "vals": [12,345,3,1,45,14,4,52,54,23,235,21,57,3,87]})
# 分组计算top-3的和  

df.groupby("grps")["vals"].nlargest(3).sum(level=0)
grps
a    409
b    156
c    345
Name: vals, dtype: int64
# A在[1，100)之间，步长为10计算各个区间的和

df.groupby(pd.cut(df["A"], np.arange(0, 101, 10)))["B"].sum()

# vals中的负值用均值替换

def replace(group):
    mask = group < 0
    group[mask] = group[~mask].mean()
    return group

df.groupby(["grps"])["vals"].transform(replace)

# 滑动窗口大小为3以内的均值

df = pd.DataFrame({"group": list("aabbabbbabab"),
                       "value": [1, 2, 3, np.nan, 2, 3, 
                                 np.nan, 1, 7, 3, np.nan, 8]})
   group  value
0      a    1.0
1      a    2.0
2      b    3.0
3      b    NaN
4      a    2.0
5      b    3.0
6      b    NaN
7      b    1.0
8      a    7.0
9      b    3.0
10     a    NaN
11     b    8.0
# 结果为如下：

0     1.000000
1     1.500000
2     3.000000
3     3.000000
4     1.666667
5     3.000000
6     3.000000
7     2.000000
8     3.666667
9     2.000000
10    4.500000
11    4.000000
g1 = df.groupby(["group"])["value"]              # group values  
g2 = df.fillna(0).groupby(["group"])["value"]    # fillna, then group values
s = g2.rolling(3, min_periods=1).sum() / g1.rolling(3, min_periods=1).count() # compute means
s.reset_index(level=0, drop=True).sort_index()  # drop/sort index
```

#### pivot
``` python
# pivot 包含3个参数index, columns, values。

from collections import OrderedDict
from pandas import DataFrame
import pandas as pd
import numpy as np
table = OrderedDict((
    ("Item", ["Item0", "Item0", "Item1", "Item1"]),
    ("CType",["Gold", "Bronze", "Gold", "Silver"]),
    ("USD",  ["1$", "2$", "3$", "4$"]),
    ("EU",   ["1€", "2€", "3€", "4€"])
))
d = DataFrame(table)
p = d.pivot(index="Item", columns="CType", values="USD")
CType Bronze Gold Silver
Item
Item0     2$   1$   None
Item1   None   3$     4$
# pivot 多行

p = d.pivot(index="Item", columns="CType")
p
         USD                 EU
CType Bronze Gold Silver Bronze Gold Silver
Item
Item0     2$   1$   None     2€   1€   None
Item1   None   3$     4$   None   3€     4€

# 解决index和column相同但是value有多个值，使用pivot_table
# pivot_table加入聚集函数aggregates

table = OrderedDict((
     ("Item", ["Item0", "Item0", "Item0", "Item1"]),
     ("CType",["Gold", "Bronze", "Gold", "Silver"]),
     ("USD",  [1, 2, 3, 4]),
     ("EU",   [1.1, 2.2, 3.3, 4.4])
))
d = DataFrame(table)
p = d.pivot_table(index=["Item"], columns=["CType"], values="USD", aggfunc=np.min)
p
CType  Bronze  Gold  Silver
Item
Item0     2.0   1.0     NaN
Item1     NaN   NaN     4.0
```

#### Crosstab
``` python
pd.crosstab(d["USD"], d["EU"], margins=True)
```

#### pandas 内存优化
``` python
def mem_usage(pandas_obj):
    if isinstance(pandas_obj, pd.DataFrame):
        usage_b = pandas_obj.memory_usage(deep=True).sum()
    else: 
        usage_b = pandas_obj.memory_usage(deep=True)
    usage_mb = usage_b / 1024
    return "{:03.2f} KB".format(usage_mb)


# 使用子类型优化数值列

df_train_int = df_train.select_dtypes(include=["int"]).apply(pd.to_numeric, downcast="unsigned")

df_train_float = df_train.select_dtypes(include=["float"]).apply(pd.to_numeric, downcast="float")

df_opt[df_train_int.columns] = df_train_int
df_opt[df_train_float.columns] = df_train_float


# 使用 Categoricals 优化 object 类型
# category 类型用于不同值的数量少于值的总数量的 50% 的 object 列

df_train_obj = df_train.select_dtypes(include=["object"]).copy()

df_train_converted_obj = pd.DataFrame()

for col in df_train_obj.columns:
    num_unique_values = len(df_train_obj[col].unique())
    num_total_values = len(df_train_obj[col])
    if num_unique_values / num_total_values < 0.5:
        df_train_converted_obj.loc[:,col] = df_train_obj[col].astype("category")
    else:
        df_train_converted_obj.loc[:,col] = df_train_obj[col]

df_opt[df_train_converted_obj.columns] = df_train_converted_obj


# 查看数值类型

dtypes = df_opt.drop("date",axis=1).dtypes
dtypes = df_opt.dtypes
dtypes_col = dtypes.index
dtypes_type = [i.name for i in dtypes.values]
column_types = dict(zip(dtypes_col, dtypes_type))
# {'age': 'uint8', 'workclass': 'category', 'fnlwgt': 'uint32', 'education': 'category', 'education_num': 'uint8', 'marital_status': 'category', 'occupation': 'category', 'relationship': 'category', 'race': 'category', 'gender': 'category', 'capital_gain': 'uint32', 'capital_loss': 'uint16', 'hours_per_week': 'uint8', 'native_country': 'category', 'income_bracket': 'category'}
# 读取时指定类型

read_and_optimized = pd.read_csv('adult.data', dtype=column_types, parse_dates=['date'], infer_datetime_format=True)


def reduce_mem_usage(df, verbose=True):
    numerics = ['int16', 'int32', 'int64', 'float16', 'float32', 'float64']
    start_mem = df.memory_usage().sum() / 1024 ** 2
    for col in df.columns:
        col_type = df[col].dtypes
        if col_type in numerics:
            c_min = df[col].min()
            c_max = df[col].max()
            if str(col_type)[:3] == 'int':
                if c_min > np.iinfo(np.int8).min and c_max < np.iinfo(np.int8).max:
                    df[col] = df[col].astype(np.int8)
                elif c_min > np.iinfo(np.int16).min and c_max < np.iinfo(np.int16).max:
                    df[col] = df[col].astype(np.int16)
                elif c_min > np.iinfo(np.int32).min and c_max < np.iinfo(np.int32).max:
                    df[col] = df[col].astype(np.int32)
                elif c_min > np.iinfo(np.int64).min and c_max < np.iinfo(np.int64).max:
                    df[col] = df[col].astype(np.int64)
            else:
                if c_min > np.finfo(np.float16).min and c_max < np.finfo(np.float16).max:
                    df[col] = df[col].astype(np.float16)
                elif c_min > np.finfo(np.float32).min and c_max < np.finfo(np.float32).max:
                    df[col] = df[col].astype(np.float32)
                else:
                    df[col] = df[col].astype(np.float64)
    end_mem = df.memory_usage().sum() / 1024 ** 2
    if verbose:
        print('Mem. usage decreased to {:5.2f} Mb ({:.1f}% reduction)'.format(
            end_mem, 100 * (start_mem - end_mem) / start_mem))
    return df

```
