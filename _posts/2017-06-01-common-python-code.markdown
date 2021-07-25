---
layout:     post
title:      "Python 必要代码片段"
date:       2017-05-28 17:10:00
author:     "Daniel"
header-img: "img/post-bg-python.jpg"
tags:
    - Python

---


#### 1、自定义数据类型
``` python
t = np.dtype([('name', np.str_, 40), ('numitems', np.int32), ('price', np.float32)])
itemz = np.array([('Meaning of life DVD', 42, 3.14), ('Butter', 13, 2.72)], dtype=t)
```

#### 2、打印完整数组
``` python
np.set_printoptions(threshold=np.inf)
np.arange(1000)
```

#### 3、dump数组到csv文件
``` python
a = np.asarray([ [1,2,3], [4,5,6], [7,8,9] ])
np.savetxt("foo.csv", a, delimiter=",")
```

#### 4、找到最接近的值
``` python
import numpy as np
def find_nearest(array, value):
    idx = (np.abs(array - value)).argmin()
    return array[idx]

# 有序数组

def find_nearest(array,value):
    idx = np.searchsorted(array, value, side="left")
    if idx > 0 and (idx == len(array) or math.fabs(value - array[idx-1]) < math.fabs(value - array[idx])):
        return array[idx - 1]
    else:
        return array[idx]
```

#### 5、时间
``` python
import datetime
start = datetime.datetime.now()
delta = datetime.datetime.now() - start
delta.microseconds
519000

# 获取unix时间戳

int(datetime.datetime.now().strftime("%s"))

# 三天前

three_day_ago = (datetime.datetime.now() - datetime.timedelta(days=3)).strftime('%Y-%m-%d')
# datetime.timedelta(days=0, seconds=0, microseconds=0, milliseconds=0, minutes=0, hours=0, weeks=0) 

# 相差多少天

d1 = datetime.datetime.strptime('2018-03-05 17:41:20', '%Y-%m-%d %H:%M:%S')
d2 = datetime.datetime.strptime('2018-03-02 17:41:20', '%Y-%m-%d %H:%M:%S')
delta = d1 - d2
print delta.days 

# 获取当前日期

import time
todayformat=str(time.strftime('%Y%m%d', time.localtime(time.time())))

# datetime => string

now = datetime.datetime.now()
now.strftime('%Y-%m-%d %H:%M:%S') 

# string => datetime

t_str = '2018-04-07 19:11:21'
d = datetime.datetime.strptime(t_str, '%Y-%m-%d %H:%M:%S')
```

#### 6、scrapy命令
``` python
scrapy help
scrapy version -v
scrapy startproject tutorial
scrapy genspider tutorial  tutorial.com
scrapy list
scrapy view url
scrapy parse url
scrapy shell url
scrapy runspider tutorial.py
scrapy bench
```

#### 7、拼接文件名
``` python
for suffix in "train", "valid", "test":
   filename = os.path.join(tmpdir, "ptb.%s.txt" % suffix)
```

#### 8、字典排序
``` python
my_dict = {"aa":111, "dd":444, "cc":333}
sorted(my_dict.items(), key=lambda x: x[1], reverse=True)
[('dd', 444), ('cc', 333), ('aa', 111)]
sorted(my_dict.items(), key=lambda x: x[0])
[('aa', 111), ('cc', 333), ('dd', 444)]
my_dict_sorted = sorted(zip(my_dict.values(), my_dict.keys()))

min_value = min(zip(my_dict.values(), my_dict.keys()))
max_value = max(zip(my_dict.values(), my_dict.keys()))

# OrderedDict 添加顺序有序排列，内部维护双向链表

from collections import OrderedDict
d = OrderedDict()
d['foo'] = 1
d['bar'] = 2
for key in d:
    print(key, d[key])
```

#### 9、字典取值
``` python
my_dict = {"aa":111, "dd":444, "cc":333}    
my_dict["bb"]                               
KeyError: 'bb'                                  
my_dict.get("bb", "Not Found")
'Not Found'                                             
```

#### 10、字典列表推导
``` python
good_dict = {value: name for name, value in my_dict.items() if value > 200}

good_dict = dict((key, value) for key, value in my_dict.items() if value > 200)

for k, v in good_dict.iteritems():
    print (k, '--->', v)
# iteritems和items的区别在于iteritems采用了生成器的原理，只有在需要的时候才会把值生成

clip_neg = [n if n > 0 else 0 for n in mylist]

```

#### 11、字典计算
``` python
my_dict = {"aa":111, "dd":444, "cc":333}
min(my_dict.values())
111
new_dict = zip(my_dict.values(), my_dict.keys())
min(new_dict)
(111, 'aa')
```

#### 12、map函数
map()传入的第一个参数是f，即函数对象本身。由于结果r是一个Iterator，Iterator是惰性序列，因此通过list()函数，让它把整个序列都计算出来并返回一个list。
``` python
def f(x):
     return x * x

r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
list(r)
[1, 4, 9, 16, 25, 36, 49, 64, 81]

a = [1, 2, 3, 4, 5]
print(' '.join(map(str, a))) 
```

#### 13、reduce函数
reduce把结果继续和序列的下一个元素做累积计算
``` python
from functools import reduce
def fn(x, y):
    return x * 10 + y
reduce(fn, [1, 3, 5, 7, 9])
13579
```

#### 14、filter函数
``` python
def not_empty(s):
    return s and s.strip()

def is_int(val):
    try:
        x = int(val)
        return True
    except ValueError:
        return False
        
# filter()函数用于过滤序列

list(filter(not_empty, ['A', '', 'B', None, 'C', '  ']))
['A', 'B', 'C']
```

#### 15、JSON
``` python
import json
d = dict(name='Bob', age=20, score=88)
json.dumps(d)
'{"age": 20, "score": 88, "name": "Bob"}'

# 消除空格

payload = json.dumps(d, separators=(',', ':'))
# 处理中文

json.dumps(d, ensure_ascii=False)
json_str = '{"age": 20, "score": 88, "name": "Bob"}'
json.loads(json_str)
{'age': 20, 'score': 88, 'name': 'Bob'}

class Student(object):
    def __init__(self, name, age, score):
        self.name = name
        self.age = age
        self.score = score
    
    def __repr__(self):
        return 'Student({})'.format(self.name)    


def student2dict(std):
    return {
	'name': std.name,
	'age': std.age,
	'score': std.score
    }

def dict2student(d):
    return Student(d['name'], d['age'], d['score'])

print(json.dumps(s, encoding='utf-8', ensure_ascii=False, default=student2dict))
{"age": 20, "name": "Bob", "score": 88}

# 通常class的实例都有一个dict属性，它就是一个dict，用来存储实例变量

# print(json.dumps(s, default=lambda obj: obj.__dict__))

json_str = '{"age": 20, "score": 88, "name": "Bob"}'
print(json.loads(json_str, object_hook=dict2student))
```

#### 16、序列化和反序列化pickle
``` python
import pickle
d = dict(name='Bob', age=20, score=88)
f = open('dump.txt', 'wb')
pickle.dump(d, f)
f.close()

f = open('dump.txt', 'rb')
d = pickle.load(f)
f.close()
d
{'age': 20, 'score': 88, 'name': 'Bob'}
```

#### 17、命令行传入参数
``` python
def main(url, query, outfile):
    pass

def str2bool(v):
    if v.lower() in ('yes', 'true', 't', 'y', '1'):
        return True
    elif v.lower() in ('no', 'false', 'f', 'n', '0'):
        return False
    else:
        raise argparse.ArgumentTypeError('Unsupported value encountered.')

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('-u', dest='url', type=str, default='www.baidu.com', help='input url')
    parser.add_argument('-d', dest='query', type=str)
    parser.add_argument('-o', dest='outfile', type=str)
    parser.add_argument('-sharding', type=str2bool, default=False) 
    options = parser.parse_args()
    main(**vars(options))
```


#### 18、拷贝列表
``` python
items = range(10)
copy_items = items[::] 
copy_items =items[:]
```


#### 19、字典中的键映射多个值
``` python
data = [('foo', 10), ('bar', 20), ('foo', 39), ('bar', 49)]
groups = {}
for (k, v) in data:
    groups.setdefault(k, []).append(v) 

for (k, v) in data:
    groups[v] = groups.get(v, [])
    groups[v].groups(k)

for (k, v) in data:
    groups[v] = groups.get(v, []) + [k]	

from collections import defaultdict
# 接受内建函数list作为参数

groups = defaultdict(list)
for (key, value) in data:
        groups[key].append(value)    
```


#### 20、双向队列
```python
from collections import deque
names = deque(['raymond', 'rachel', 'matthew', 'roger'])
names.popleft()
names.appendleft('mark')
```


#### 21、打包与解包
``` python
p = (4, 5)
x, y = p

record = ('ACME', 50, 123.45, (12, 18, 2012))
name, *_, (*_, year) = record

record = ('Dave', 'dave@example.com', '773-555-1212', '847-555-1212')
name, email, *phone_numbers = record

# 函数unpack

def foo(x, y):
   print x, y

alist = [1, 2]
adict = {'x': 1, 'y': 2}
foo(*alist) 
foo(**adict)
```


#### 22、打平List
``` python
list_1 = [[1, 2], [3, 4, 5], [6, 7], [8], [9]]
# 方法1

list_1 = [[1, 2], [3, 4, 5], [6, 7], [8], [9]]
for _ in list_1:
   list_2 += _

# 方法2

list_2 = [i for k in list_1 for i in k]

# 方法3

sum(list_1, [])

# 方法4

from itertools import chain
list(chain.from_iterable(list_1))

# 方法5

from itertools import chain
list(chain(*list_1))

# 方法6

func = lambda x: [y for t in x for y in func(t)] if type(x) is list else [x]
func(list_1)

```


#### 23、删除序列重复元素并保持顺序
``` python
def dedupe(items, key=None):
    seen = set()
    for item in items:
        val = item if key is None else key(item)
        if val not in seen:
            yield item
            seen.add(val)
            
a = [ {'x':1, 'y':2}, {'x':1, 'y':3}, {'x':1, 'y':2}, {'x':2, 'y':4}]           
list(dedupe(a, key=lambda d: (d['x'], d['y'])))
list(dedupe(a, key=lambda d: d['x']))
```


#### 24、wordcount
``` python
from collections import Counter
names = ['alice','bob','bob','candice','candice']
names_counts = Counter(names)
# 出现频率最高的3个

top_three = names_counts.most_common(3)

names_counts.update(more_names)

from collections import defaultdict
names_counts = defaultdict(lambda: 0)
for k in names:
    names_counts[k] += 1
dict(names_counts)
```


#### 25、生成器yield表达式
``` python
def get_indices(string):
    for index, letter in enumerate(string):
        if letter == 'a':
            yield index
results = get_indices('this is a test to check a')
results_list = list(results)
```


#### 26、导入本地方法
``` python
sys.path.append('../common')
from report_common import *
```


#### 27、读写文件
``` python
# 获取当前文件夹下的所有文件

from os import listdir
from os.path import isfile, join
files = [f for f in listdir(stop_word_dir) if isfile(join(stop_word_dir, f))]


# 以某字符串结尾的文件

import os
topdir = '.'
ext = '.txt'
for dirpath, dirnames, files in os.walk(topdir):
    for name in files:
        if name.lower().endswith(ext):
            print(os.path.join(dirpath, name))

# 获取所有py文件

from pathlib import Path
py_files = list(Path('.').glob("*.py"))
from glob import glob
py_files = list(glob('*.py'))


with open(file_name, "w", encoding="utf-8") as f_zvalue:
    for m in range(self.M):
        pass
        
f = open('test.txt')
for line in f.readlines():
    if not line.strip():
        continue
    line = line.strip('\n').line.split(',')
f.close()


f = open('myfile.txt', 'w')
f.write('another hello world!')
f.close()


# 删除文件 / 文件夹

import os
os.path.isfile('test.txt')
os.remove('test.txt')
os.path.exists('test.txt')

os.path.isdir('test_folder')
os.rmdir('test_folder')
os.path.exists('test_folder')

# 绝对路径

os.path.abspath(".")

# 获取当前目录

import os
cur_dir = os.getcwd()
from pathlib import Path
cur_dir = Path.cwd()

# 创建目录

os.mkdir("test_folder")
Path("test_folder").mkdir(parents=True, exist_ok=True)

# 移动文件

target_folder = Path("py_bak")
target_folder.mkdir(parents=True, exist_ok=True)
source_folder = Path('.')
py_files = source_folder.glob('*.py')
for py in py_files:
    filename = py.name
    target_path = target_folder.joinpath(filename)
    print(f"** 移动文件 {filename}")
    print("目标文件存在:", target_path.exists())
    py.rename(target_path)
    print("目标文件存在:", target_path.exists(), '\n')

# 复制文件

import shutil
source_file = "target_folder/hello.txt"
target_file = "hello2.txt"
target_file_path = Path(target_file)
print("* 复制前，文件存在:", target_file_path.exists())
shutil.copy(source_file, target_file)
print("* 复制后，文件存在:", target_file_path.exists())    

```


#### 28、set集合操作
``` python
a = set(['a', 'b', 'c', 'd'])
b = set(['c', 'd', 'e', 'f'])
c = set(['a', 'c'])
# Intersection

print(a & b)
# Subset

print(c < a)
# Difference

print(a - b)
# Symmetric Difference

print(a ^ b)
# Union

print(a | b)

"""using methods instead of operators which take any iterable as a second arg"""

a = {'a', 'b', 'c', 'd'}
b = {'c', 'd', 'e', 'f'}
c = {'a', 'c'}
print(a.intersection(["b"]))
print(a.difference(["foo"]))
print(a.symmetric_difference(["a", "b", "e"]))
print(a.issuperset(["b", "c"]))
print(a.issubset(["a", "b", "c", "d", "e", "f"]))
print(a.isdisjoint(["y", 'z']))
print(a.union(["foo", "bar"]))
a.intersection_update(["a", "c", "z"])
print(a)  
```


#### 29、通过某个关键字排序（或分组）一个字典列表
``` python
rows = [
    {'fname': 'Brian', 'lname': 'Jones', 'uid': 1003},
    {'fname': 'David', 'lname': 'Beazley', 'uid': 1002},
    {'fname': 'John', 'lname': 'Cleese', 'uid': 1001},
    {'fname': 'Big', 'lname': 'Jones', 'uid': 1004}
]
# sort的排序是inplace, sorted是返回一个排序后的list，原list不变

# 排序

from operator import itemgetter
rows_by_fname = sorted(rows, key=itemgetter('fname'))
rows_by_fname = sorted(rows, key=lambda r: r['fname'])
rows_by_lfname = sorted(rows, key=itemgetter('lname','fname'))

min(rows, key=itemgetter('uid'))
max(rows, key=itemgetter('uid'))

# 分组

from itertools import groupby
rows.sort(key=itemgetter('fname'))
# Iterate in groups

for fname, items in groupby(rows, key=itemgetter('fname')):
    print(fname)
    for i in items:
        print(' ', i)
        
# 排序

users = [User(23), User(3), User(99)]
print(sorted(users, key=lambda u: u.user_id))
from operator import attrgetter
sorted(users, key=attrgetter('user_id'))
sorted(users, key=attrgetter('user_id'), reverse=True)
```


#### 30、切片反转
``` python
a='big'
a[::-1]

list1 = [4,5,6]
list1[::-1]
```


#### 31、字符串分割 split、rsplit和splitlines
``` python
line = 'asdf fjdk; afed, fjek,asdf, foo'
import re
re.split(r'[;,\s]\s*', line)

# 只分割了一次

'1,2,3'.split(',', 1)  
['1', '2,3']   
'   1    2   3  \n'.split()
['1', '2', '3']
'ab c\n\nde fg\rkl\r\n'.splitlines()
['ab c', '', 'de fg', 'kl']
'One line\n'.split('\n')
['One line', '']
'Two lines\n'.splitlines()
['Two lines']
```


#### 32、字符串分组匹配
``` python
date_pat = re.compile(r'(\d+)/(\d+)/(\d+)')
m = date_pat.match('11/27/2012')
m.group(0)
'11/27/2012'
m.group(1)
'11'
m.group(2)
'27'
m.group(3)
'2012'
m.groups()
('11', '27', '2012')
month, day, year = m.groups()

for m in date_pat.finditer(text):
    print(m.groups())
```


#### 33、字符串替换
``` python
text = 'Today is 11/27/2012. PyCon starts 3/13/2013.'
import re
re.sub(r'(\d+)/(\d+)/(\d+)', r'\3-\1-\2', text)
'Today is 2012-11-27. PyCon starts 2013-3-13.'
```


#### 34、拼接字符串
``` python
data = ['ACME', 50, 91.1]
','.join(str(d) for d in data)
','.join(map(str, data))

print(a, b, c, sep=':')

s = '{name} has {n} messages.'
s.format(name='Guido', n=37)
name = 'Guido'
n = 37
s.format_map(vars())

```


#### 35、正则匹配
``` python
import re
def nonsense_word(txt):
    patt = [
        r"[一二三四五六七八九十百千万零]", r"^第", r"章$"
    ]
    return any(re.search(pat, txt) for pat in patt)

s1="HI This is regular expressions topic"
res=re.findall(r"\b\w{2}",s1)
print(res)
['HI', 'Th', 'is', 're', 'ex', 'to']
```


#### 36、配置日志
``` python
logging.basicConfig(format='%(asctime)s : %(levelname)s : %(message)s', level=logging.INFO)
logger = logging.getLogger()
```


#### 37、三目操作符
``` python
x = 10 if y == 9 else 20

1 == 1 and 2 or 3   返回2
1 == 2 and 2 or 3   返回3
1 and 2 and 3   返回3
1 and 2 and ''   返回''
'' and 2 and 0  返回''
# 如果都为真则返回最后一个值，如果其中某些值为假，则返回第一个为假的值

1 or '' or 0    返回1
'' or 0 or []    返回[]
# 如果都为假返回最后一个值，如果其中某些值为真，则返回第一个为真的值

dic = dic1.update(dic2) or dic1
value = value or {}

if embedding_name is None:
    embedding_name = name
embedding_name = embedding_name or name

if embedding_dim == "auto":
    embedding_dim =  6 * int(pow(vocabulary_size, 0.25))
embedding_dim = embedding_dim == "auto" and 6 * int(pow(vocabulary_size, 0.25)) or embedding_dim
```


#### 38、初始化列表
``` python
list = [0] * 10
bag_of_bags = [[0] for _ in range(5)]
# [[0], [0], [0], [0], [0]]
```


#### 39、查询mysql
``` python
from sqlalchemy import create_engine
engine = create_engine("mysql+pymysql://readonly:passwd@127.0.0.1:3306/database?charset=utf8")
data_df = pd.read_sql_query(sql, engine)
```


#### 40、实现switch
``` python
options = {'this': 1, 'that': 2, 'there': 3}
the_thing = options.get(something, 4)

from collections import defaultdict
default_options = defaultdict(lambda: 4, {'this': 1, 'that': 2, 'there': 3})
the_thing = default_options[something]

def f(x):
  return {
	0:"zero",
	1:"one"
  }.get(x, "xxx") 

def switch_dict(operator, x ,y):
	return {
		'add' : lambda: x + y,
		'sub' : lambda: x - y,
		'mul' : lambda: x * y,
		'div' : lambda: x / y,
	}.get(operator, lambda: None)()
```


#### 41、python文档之查看帮助文档
``` python
# 使用time模块,使用time模块的localtime函数,使用range类 

help(time)
help(time.localtime())
help(range) 

print(time.__doc__)
print(time.localtime().__doc__)
print(range.__doc__) 

print(dir(time))  
print(dir(time.localtime()))
print(dir(range))  
```

#### 42、特征处理
来自[sklearn做特征工程](https://blog.csdn.net/fuqiuai/article/details/79496005)
``` python
import pandas as pd
import numpy as np
from numpy import vstack, array, nan
from sklearn.datasets import load_iris

from sklearn import preprocessing
from sklearn import feature_selection
from sklearn.linear_model import LogisticRegression
from sklearn.ensemble import GradientBoostingClassifier
from sklearn.decomposition import PCA
from sklearn.discriminant_analysis import LinearDiscriminantAnalysis as LDA


# 导入IRIS数据集

iris = load_iris()
features = iris.data
labels = iris.target

'''
1.数据预处理
'''

# 1.1 无量纲化：将不同规格的数据转换到同一规格

# 1.1.1 标准化：将服从正态分布的特征值转换成标准正态分布（对列向量处理）

# print(np.mean(features, axis=0))

# print(np.std(features, axis=0))

# 均值为0，方差为1

features_new = preprocessing.StandardScaler().fit_transform(features)
# 1.1.2 区间缩放：将特征值缩放到[0, 1]区间的数据（对列向量处理）

# 中心化稀疏(矩阵)数据会破坏数据的稀疏结构，但可以缩放多个特征在不同量级范围的稀疏输入

features_new = preprocessing.MinMaxScaler().fit_transform(features)
features_new = preprocessing.MaxAbsScaler().fit_transform(features)
# 1.1.3 归一化：将行向量转化为“单位向量”（对单个样本处理），用于文本分类和内容聚类

features_new = preprocessing.Normalizer().fit_transform(features)

# 1.2 对定量特征二值化:设定一个阈值，大于阈值的赋值为1，小于等于阈值的赋值为0

features_new = preprocessing.Binarizer(threshold=3).fit_transform(features)

# 1.3 对定性（分类）特征编码(也可用pandas.get_dummies函数)

enc = preprocessing.OneHotEncoder()
enc.fit([[0, 0, 3],
         [1, 1, 0],
         [0, 2, 1],
         [1, 0, 2]])
# print(enc.transform([[0, 1, 3]]))

# print(enc.transform([[0, 1, 3]]).toarray())

le = preprocessing.LabelEncoder()
le.fit(["paris", "paris", "tokyo", "amsterdam"])
# 类别变量编码为整数

le.transform(["tokyo", "tokyo", "paris"]) 
list(le.inverse_transform([2, 2, 1]))

import pandas as pd
from sklearn.preprocessing import LabelEncoder
from sklearn.pipeline import Pipeline

fruit_data = pd.DataFrame({
    'fruit':  ['apple','orange','pear','orange'],
    'color':  ['red','orange','green','green'],
    'weight': [5,6,3,4]
})
MultiColumnLabelEncoder(columns = ['fruit','color']).fit_transform(fruit_data)
fruit_data[['fruit','color']]=fruit_data[['fruit','color']].apply(LabelEncoder().fit_transform)

# 1.4 缺失值计算(也可用pandas.fillna函数)

imp = preprocessing.Imputer(missing_values='NaN', strategy='mean', axis=0)
features_new = imp.fit_transform(vstack((array([nan, nan, nan, nan]), features)))

# 1.5 数据变换

# 1.5.1 基于多项式变换（对行变量处理）

features_new = preprocessing.PolynomialFeatures().fit_transform(features)
# 1.5.2 基于自定义函数变换，以log函数为例

features_new = preprocessing.FunctionTransformer(np.log1p).fit_transform(features)

'''
2.特征选择
'''
# 2.1 Filter

# 2.1.1 方差选择法，选择方差大于阈值的特征

features_new = feature_selection.VarianceThreshold(threshold=0.3).fit_transform(features)
# 2.1.2 卡方检验,选择K个与标签最相关的特征

features_new = feature_selection.SelectKBest(feature_selection.chi2, k=3).fit_transform(features, labels)

# 2.2 Wrapper

# 2.2.1 递归特征消除法，这里选择逻辑回归作为基模型，n_features_to_select为选择的特征个数

features_new = feature_selection.RFE(estimator=LogisticRegression(), n_features_to_select=2).fit_transform(features, labels)

# 2.3 Embedded

# 2.3.1 基于惩罚项的特征选择法,这里选择带L1惩罚项的逻辑回归作为基模型

features_new = feature_selection.SelectFromModel(LogisticRegression(penalty="l1", C=0.1)).fit_transform(features, labels)
# 2.3.2 基于树模型的特征选择法,这里选择GBDT模型作为基模型

features_new = feature_selection.SelectFromModel(GradientBoostingClassifier()).fit_transform(features, labels)

'''
3.降维
'''
# 3.1 主成分分析法（PCA）,参数n_components为降维后的维数

features_new = PCA(n_components=2).fit_transform(features)

# 3.2 线性判别分析法（LDA）,参数n_components为降维后的维数

features_new = LDA(n_components=2).fit_transform(features, labels)
```

#### 43、按条件调用函数
``` python
def product(a, b):
    return a * b

def subtract(a, b):
    return a - b

b = True
print((product if b else subtract)(1, 1))
```

#### 44、划分数据集
``` python
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn import datasets
from sklearn import svm
iris = datasets.load_iris()
X_train, X_test, y_train, y_test = train_test_split(iris.data, iris.target, test_size=0.4, random_state=0)
```

#### 45、距离计算
``` python
## hamming

from sklearn.metrics import hamming_loss
y_pred = [1, 2, 3, 4]
y_true = [2, 2, 3, 4]
hamming_loss(y_true, y_pred)
0.25

## jaccard

import numpy as np
from sklearn.metrics import jaccard_similarity_score
y_pred = [0, 2, 1, 3]
y_true = [0, 1, 2, 3]
jaccard_similarity_score(y_true, y_pred)
0.5
jaccard_similarity_score(y_true, y_pred, normalize=False)
2

## euclidean

from sklearn.metrics.pairwise import euclidean_distances
X = [[0, 1], [1, 1]]
euclidean_distances(X, X)
array([[0., 1.],
       [1., 0.]])
euclidean_distances(X, [[0, 0]])
array([[1.        ],
       [1.41421356]])

# cosine

from sklearn.metrics.pairwise import cosine_similarity
a=[[1,3,2],[2,2,1]]
cosine_similarity(a)
array([[1.        , 0.89087081],
       [0.89087081, 1.        ]])

from sklearn.metrics.pairwise import pairwise_distances
pairwise_distances(a, metric="cosine")
array([[0.        , 0.10912919],
       [0.10912919, 0.        ]])
```

#### 46、requests
``` python
import requests
param = {'name':'jyx','age':19}
response = requests.get('http://httpbin.org/get', params=param)
print(response.text)
print(response.json())


print(type(response.status_code), response.status_code)
print(type(response.headers), response.headers)
print(type(response.cookies), response.cookies)
print(type(response.url), response.url)
print(type(response.history), response.history)

# 异步请求

import grequests
url = "http://xxx"
times = 100
rs = (grequests.get(url) for i in range(times)) 
rets = grequests.map(rs) 
res = [ret.json() for ret in rets if ret and ret.status_code == 200]
```

#### 47、分析redis key分布
``` python
import redis
from collections import defaultdict

uc_r = redis.Redis(host="127.0.0.1", port=6379, decode_responses=True)
pipe = uc_r.pipeline()

prefix_counts = defaultdict(lambda: 0)

for i in range(100000):
	for j in range(1000):
		pipe.randomkey()
	result_keys = pipe.execute()

	for key in result_keys:
		if key:
			prefix = key.rsplit("_", 1)[0]
			prefix_counts[prefix] += 1

dict(prefix_counts)

# 查看具体的key

for key in r.scan_iter("key_prefix_*", count=10):
	print(key) 
```

#### 48、json 格式化
``` python
echo '{"foo": "lorem", "bar": "ipsum"}' | python -m json.tool
curl https://randomuser.me/api/ | python -m json.tool
```

#### 49、拼接list
``` python
listone = [1,2,3]
listtwo = [4,5,6]
mergedlist=listone + listtwo
import itertools
mergedlist=list(itertools.chain(listone, listtwo))
```

#### 50、list append和extend区别
``` python
x = [1, 2]
x.append([4,5])
[1, 2, [4, 5]]
x = [1, 2, 3]
x.extend([4, 5])
[1, 2, 3, 4, 5]
```

#### 51、随机地从列表中抽取变量
``` python
foo = ['a', 'b', 'c', 'd', 'e']
from random import choice
print choice(foo)
```


#### 52、加载pb文件打印tensorflow变量
``` python
import tensorflow as tf
from tensorflow.python.platform import gfile
GRAPH_PB_PATH = './minimal_graph.proto'
with tf.Session() as sess:
   print("load graph")
   with gfile.FastGFile(GRAPH_PB_PATH,'rb') as f:
       graph_def = tf.GraphDef()
   graph_def.ParseFromString(f.read())
   sess.graph.as_default()
   tf.import_graph_def(graph_def, name='')
   graph_nodes=[n for n in graph_def.node]
   names = []
   for t in graph_nodes:
      names.append(t.name)
   print(names)
   for op in sess.graph.get_operations():
      print(op.name, op.values())
 
# saved model

import tensorflow as tf
from tensorflow.python.platform import gfile
from tensorflow.core.protobuf import saved_model_pb2
from tensorflow.python.util import compat

with tf.Session() as sess:
  model_filename ='./tf_mnist/saved_model.pb'
  with gfile.FastGFile(model_filename, 'rb') as f:
    data = compat.as_bytes(f.read())
    sm = saved_model_pb2.SavedModel()
    sm.ParseFromString(data)
    g_in = tf.import_graph_def(sm.meta_graphs[0].graph_def)

train_writer = tf.summary.FileWriter('./tf_logs')
train_writer.add_graph(sess.graph)
train_writer.flush()
train_writer.close()

 
# tensorboard查看

summary_writer = tf.summary.FileWriter("./tf_logs", sess.graph)
# shell

tensorboard --logdir="./tf_logs"
```

####  53、CLI 检查并执行 SavedModel
``` python
saved_model_cli show --dir tf_mnist\ --all
```

#### 54、删除连续重复项的元素
``` python
l=[1,1,1,1,1,1,2,3,4,4,5,1,2]
from itertools import groupby
[x[0] for x in groupby(l)]
[1, 2, 3, 4, 5, 1, 2]

from operator import itemgetter
map(itemgetter(0), groupby(l))
[1, 2, 3, 4, 5, 1, 2]
``` 

#### 55、ip2long
``` python
sum([256 ** i * int(p) for i, p in enumerate(ip.split('.')[::-1])])
```


#### 56、参数解包
``` python
def product(a, b):
    return a * b

argument_tuple = (1, 1)
argument_dict = {'a': 1, 'b': 1}
print(product(*argument_tuple))
print(product(**argument_dict))
```


#### 57、struct
``` python
from collections import namedtuple
websites = [
    ('Sohu', 'http://www.google.com/', '张朝阳'),
    ('Sina', 'http://www.sina.com.cn/', '王志东'),
    ('163', 'http://www.163.com/', '丁磊')
]
Website = namedtuple('Website', ['name', 'url', 'founder'])
for w in websites:
    website = Website._make(w)
    print(website)
    
Card = namedtuple('Card', ['rank', 'suit'])

class Deck:
    ranks = [str(n) for n in range(2, 11)] + list('JQKA')
    suits = 'spades diamonds clubs hearts'.split()
    def __init__(self):
        self._cards = [Card(rank, suit) for suit in self.suits for rank in self.ranks]
	
    def __len__(self):
        return len(self._cards)
	
    def __getitem__(self, position):
        return self._cards[position]

card_a = Card('A', 'spades')
print(card_a)

deck = Deck()
len(deck)

print(deck[0])
print(deck[-1])
for card in deck:
    print(card)    


class SparseFeat(namedtuple('SparseFeat',
                            ['name', 'vocabulary_size', 'embedding_dim', 'use_hash', 'dtype', 'embedding_name', 'group_name'])):
    __slots__ = ()

    def __new__(cls, name, vocabulary_size, embedding_dim=4, use_hash=False, dtype="int32", embedding_name=None, group_name=DEFAULT_GROUP_NAME):
        if embedding_name is None:
            embedding_name = name
        if embedding_dim == "auto":
            embedding_dim =  6 * int(pow(vocabulary_size, 0.25))
        return super(SparseFeat, cls).__new__(cls, name, vocabulary_size, embedding_dim, use_hash, dtype, embedding_name, group_name)

    def __hash__(self):
        return self.name.__hash__()

    def __eq__(self, other):
        if self.name == other.name and self.embedding_name == other.embedding_name:
            return True
        return False

    def __repr__(self):
        return 'SparseFeat:' + self.name    
```

#### 58、hash、eq
``` python
class Foo(object):
    def __init__(self, value):
        self.bar = value
    def __eq__(self, other):
        return self.bar == getattr(other, 'bar')   
    def __hash__(self):
        return int(self.bar)    
    def __repr__(self):
        return '{}'.format(self.bar)

item1 = Foo(15)
item2 = Foo(15)
item3 = Foo(5)
lst = [item1, item2, item3]
print(set(lst))   
```


#### 59、table kkv
``` python
from collections import defaultdict
tree = lambda: defaultdict(tree)
users = tree()
users['harold']['username'] = 'chopper'
users['matt']['password'] = 'hunter2' 

{“harold”: {“username”: “chopper”}, “matt”: {“password”: “hunter2”}} 

segment_space = defaultdict(lambda: defaultdict(list))
segment_weights = defaultdict(lambda: defaultdict(float))
total_weights = defaultdict(float)
```

#### 60、keras 打印模型结构
``` python
brew install graphviz
python3 -m pip install pydot
python3 -m pip install graphviz
python3 -m pip install pydot_ng
python3 -m pip install pydotplus

from keras.utils import plot_model
plot_model(model, to_file='model.png', show_shapes=True)
```

#### 61、xpath nodeValue
``` python
https://www.aminer.cn/expert/568b998645ce10fa577626a6
var name_list = $x("//div[@class='expert-item-content team-v2 shadow-10 bg-color-white']//strong[@class='ng-binding']//text()")
name_list.map(function(value, index){console.log("index:" + index + ", value: " + value.nodeValue)})

# 爬取小说标签
import requests 
from lxml import html 
url = "https://www.biqi.org/tag/{}.html"

tags = []
for i in range(1, 23):
    page_url = url.format(i)
    response = requests.get(page_url, headers={})
    content = response.content.decode("utf8")
    selector = html.fromstring(content) 
    tag_list = selector.xpath("//table[@class='tagCol']/tbody/tr/td/a/text()")
    url_list = selector.xpath("//table[@class='tagCol']/tbody/tr/td/a/@href")
    num_list = selector.xpath("//table[@class='tagCol']/tbody/tr/td/b/text()")
    print(page_url, tag_list, num_list)
    tags.extend(tag_list)

print(tags)


import requests 
from lxml import html 
url = "https://www.xiagu.org/tag/id/24.html"
response = requests.get(url, headers={})
content = response.content.decode("utf8")
selector = html.fromstring(content) 
sub_divs = selector.xpath("//li[@class='subject-item']/div/div[@class='pub']")
for div in sub_divs:
    # html.tostring(div, encoding='utf8').decode('utf8')
    keys = div.xpath("./text()")
    author = div.xpath('./a[1]/text()')[0]
    category = div.xpath('./a[2]/text()')[0]
    # values = div.xpath("./a/text()")
    urls = div.xpath("./a/@href")
    print(keys[0], author, keys[1], category, keys[2])
    # best answer
    # target = div.xpath("string(.)")


# BeautifulSoup爬取
import requests
from bs4 import BeautifulSoup
from lxml import html 
url = "https://www.biqi.org/tag/{}.html"

tags = []
for i in range(1, 23):
    page_url = url.format(i)
    html = requests.get(page_url).content
    soup = BeautifulSoup(html, 'lxml')
    items = soup.find_all('table', attrs={'class': "tagCol"})
    for item in items:
        for t in item.find_all('a'):
            # <a href="/tag/id/829.html">女扮男装</a>
            sub_url = t.attrs['href']
            name = t.string
            tags.append((sub_url, name))
            print(i, sub_url, name)
```

#### 62、描述符
``` python
# 描述符：实现了 __get__()、__set__()、__delete__() 其中至少一个方法的类

class NotNegative():
    def __init__(self,name):
        self.name = name

    def __set__(self, instance, value):
        if value < 0:
            raise ValueError(self.name+' must be >= 0')
        instance.__dict__[self.name] = value

class Product():
    quantity = NotNegative('quantity')
    price = NotNegative('price')

    def __init__(self,name,quantity,price):
        self.name = name
        self.quantity = quantity
        self.price = price

    def __repr__(self):
        return "<Product: {}, quantity:{}, price:{}>".format(
                self.name, self.quantity, self.price)    

book1 = Product('mybook1', 3, 4)
book2 = Product('mybook2', 5, 6)
```

#### 63、下划线模式和命名约定
``` python
下划线模式和命名约定
_var: 以单个下划线开头的变量或方法仅供内部使用
var_: 避免与Python关键字产生命名冲突
__var:双下划线前缀会导致Python解释器重写属性名称，以避免子类中的命名冲突
__var__:双下划线前缀和后缀包围的变量不会被Python解释器修改
_:单个独立下划线是用作一个名字，来表示某个变量是临时的或无关紧要的
```

#### 64、ngram
``` python
input_list = ['all', 'this', 'happened', 'more', 'or', 'less']
# Bigrams

zip(input_list, input_list[1:])
# Trigrams

zip(input_list, input_list[1:], input_list[2:])

def find_ngrams(input_list, n):
    return zip(*[input_list[i:] for i in range(n)])
```

#### 65、星号和双星号
``` python 
l1 = ['kaguya', 'miyuki']
l2 = ['chika', 'ishigami']
[*l1, *l2]
# ['kaguya', 'miyuki', 'chika', 'ishigami']

d1 = {'name': 'rimuru'}
d2 = {'kind': 'slime'}
{**d1, **d2}
# {'name': 'rimuru', 'kind': 'slime'}
```

#### 66、类型的最大值和最小值
``` python
import numpy as np
int_types = ["uint8", "int8", "int16"]
for it in int_types:
    print(np.iinfo(it))
```

####  67、list中的最小和最大索引 argmin 和 argmax
``` python
def arg_min(lst):
   return min(range(len(lst)), key=lst.__getitem__)

def arg_max(lst):
   return max(range(len(lst)), key=lst.__getitem__)

```

#### 68、代码耗时
``` python
import time
import functools
def time_profile(func):
    """
    时间统计
    :param func:
    :return:
    """
    @functools.wraps(func)
    def wrapper(*args, **kw):
        start = time.time()
        result = func(*args, **kw)
        end = time.time()
        logger.info("[%s] costs time is %.2f s" % (func.__name__, end - start))
        return result
    return wrapper
    
@time_profile
def load_graph():
   pass

# 使用contextmanager

from contextlib import contextmanager
from time import perf_counter
@contextmanager
def timeblock(label):
    tic = perf_counter()
    try:
        yield
    finally:
        toc = perf_counter()
        print("%s costs time is %.2f" % (label, toc - tic))

# 代码块耗时测试

with timeblock('counting'):
    t = [i for i in range(1000000)]

```


#### 69、tornado example
``` python
import tornado.ioloop
import tornado.web
import tornado.httpclient
import tornado.httpserver
import json
import random

# curl "http://localhost:7070/recommend?uid=8&bid=12"

# curl "http://localhost:7070/random?uid=8&bid=12"

# curl -d "name=admin&pwd=12345678" http://localhost:7070/post_file

http_client = tornado.httpclient.HTTPClient()

def predict(uid, bid):
	print(uid, bid)
	res = [1, 2, 3]
	return res

class SearchHandler(tornado.web.RequestHandler):
	def get(self):
		res = predict(self.get_argument("uid"), self.get_argument("bid"))
		self.set_header("Content-Type", "text/html; charset=UTF-8")
		self.write(json.dumps(res, ensure_ascii=False, indent=4))


def get_random_bid(request):
    book_id = request.get_argument("bid", "bookid")
    print(type(request), book_id)
    bid = random.randint(0, 100)
    return {"book_id": bid}


class RandomHandler(tornado.web.RequestHandler):
    def get(self):
        res = {}
        try:
            res = get_random_bid(self)
        except Exception as e:
            res["error"] = str(e)
            raise e
        self.set_header("Content-Type", "text/plain; charset=UTF-8")
        self.write(json.dumps(res, ensure_ascii=False, indent=4))


def post(obj):
    print(json.dumps(obj, indent=4, ensure_ascii=False))
    while True:
        try:
	        # 异步HTTP客户端

            r = http_client.fetch(tornado.httpclient.HTTPRequest("http://"+IP_PORT+"/update", method='POST', headers={"Content-Type": "application/json"}, body=json.dumps(obj)))
            print(r.body.decode())
            break
        except Exception as e:
            print("[EXCEPTION]",e)


class PostHandler(tornado.web.RequestHandler):
    def post(self):
        name=self.get_body_argument("name")
        pwd = self.get_body_argument("pwd")
        res = {"name":name, "pwd": pwd}
        post(res)

handlers = [
    (r"/recommend", SearchHandler),
    (r"/random", RandomHandler),
    (r"/post_file", PostHandler)
]

application = tornado.web.Application(handlers)

if __name__ == "__main__":
    http_server = tornado.httpserver.HTTPServer(application)
    http_server.bind(7070, '0.0.0.0')
    http_server.start(5)
    tornado.ioloop.IOLoop.current().start()
```

#### 70、ip2long
``` python
import struct
import socket

def ip2long(ip):
    return struct.unpack("!L",socket.inet_aton(ip))[0]

def long2ip(longip):
    return socket.inet_ntoa(struct.pack('!L', longip))
```

#### 71、数组截断填充
``` python
from keras.preprocessing import sequence
x_train = sequence.pad_sequences(x_train, maxlen=200, padding='post', truncating='post', value=0)
```

#### 72、多标签预测评估
``` python
from sklearn import metrics
from sklearn.metrics import classification_report

metrics.f1_score(Y_test, Y_pred, average="macro")
metrics.f1_score(Y_test, Y_pred, average="micro")
metrics.f1_score(Y_test, Y_pred, average="weighted")
metrics.f1_score(Y_test, Y_pred, average="samples")

report_predict = classification_report(y_true, y_pred, labels=CATE_NAMES, target_names=CATE_CN_NAMES)

```

#### 73、fasttext文本分类
``` python
from keras import Input, Model
from keras.layers import Embedding, GlobalAveragePooling1D, Dense

class FastText(object):
    def __init__(self, max_len, max_features, embedding_dims,
                 class_num=1, last_activation='sigmoid'):
        self.max_len = max_len
        self.max_features = max_features
        self.embedding_dims = embedding_dims
        self.class_num = class_num
        self.last_activation = last_activation

    def get_model(self):
        input = Input((self.max_len,))
        embedding = Embedding(self.max_features, self.embedding_dims, input_length=self.max_len)(input)
        x = GlobalAveragePooling1D()(embedding)
        output = Dense(self.class_num, activation=self.last_activation)(x)
        model = Model(inputs=input, outputs=output)
        return model
        

(x_train, y_train), (x_test, y_test) = imdb.load_data(num_words=max_features)
print('Pad sequences (samples x time)...')
x_train = sequence.pad_sequences(x_train, max_len=max_len)
x_test = sequence.pad_sequences(x_test, max_len=max_len)
print('x_train shape:', x_train.shape)
print('x_test shape:', x_test.shape)
print('Build model...')
model = FastText(max_len, max_features, embedding_dims).get_model()
model.compile('adam', 'binary_crossentropy', metrics=['accuracy'])
print('Train...')
early_stopping = EarlyStopping(monitor='val_acc', patience=3, mode='max')
model.fit(x_train, y_train,
          batch_size=batch_size,
          epochs=epochs,
          callbacks=[early_stopping],
          validation_data=(x_test, y_test))
print('Test...')
result = model.predict(x_test)
```

#### 74、rediscluster
``` python
from rediscluster import StrictRedisCluster
import sys

redis_nodes = [
    {'host': '127.0.0.1', 'port': 6380},
    {'host': '127.0.0.2', 'port': 6380},
]
rc = StrictRedisCluster(startup_nodes=redis_nodes)
user_cnt = 0
cache_user_list = []
for key in rc.scan_iter("c_*", count=2000):
    cache_user_list.append(key)
```

#### 75、转成 word2vec 向量查找
``` python
import json
import gensim
import numpy as np
from sklearn.preprocessing import normalize

input_file = "item_factor.txt"
format_file = "item_factor_emb.txt"

with open(input_file, "r") as input:
    item_size = sum(1 for _ in input)

with open(input_file, "r") as input:
    _, embedding_str = input.readline().split("\t")
    emb_len = len(json.loads(embedding_str))

with open(input_file, "r") as input, open(format_file, "w") as output:
    output.write(str(item_size) + " " + str(emb_len))
    for line in input.readlines():
        item_id, embedding_str = line.split("\t")
        if item_id and embedding_str:
            item_emb = np.array(json.loads(embedding_str))
            # normalized_item_emb = normalize(item_emb[:, np.newaxis], axis=0, norm="l2").ravel()

            normalized_item_emb = item_emb / np.linalg.norm(item_emb)
            item_vec = item_id + " " + " ".join(map(str, normalized_item_emb))
            output.write("\n" + item_vec)

model = gensim.models.KeyedVectors.load_word2vec_format(format_file, binary=False)  
top_sim_list = model.wv.most_similar(positive=["33333"], topn=200)

```

#### 76、找到最大或最小的N个元素
``` python
import heapq

nums = [1, 8, 2, 23, 7, -4, 18, 23, 42, 37, 2]
heapq.nlargest(3, nums)
heapq.nsmallest(3, nums)

portfolio = [
    {'name': 'IBM', 'shares': 100, 'price': 91.1},
    {'name': 'AAPL', 'shares': 50, 'price': 543.22},
    {'name': 'FB', 'shares': 200, 'price': 21.09},
    {'name': 'HPQ', 'shares': 35, 'price': 31.75},
    {'name': 'YHOO', 'shares': 45, 'price': 16.35},
    {'name': 'ACME', 'shares': 75, 'price': 115.65}
]
heapq.nsmallest(3, portfolio, key=lambda s: s['price'])
```

#### 77、有放回随机采样和无放回随机采样
``` python
seq = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
random.choices(seq, k=3)
[0, 2, 0]
random.sample(seq, k=3)
[2, 3, 4]
```

#### 78、字符串搜索
``` python
a = "i love python"
# find 如果找不到返回-1

a.find("ovo", 0, -1)
-1
# index 如果找不到抛出ValueError异常

a.index("ovo", 0, -1)
ValueError: substring not found

# in操作

'xy' in 'abxycd'
True

# count

a.count("ovo") > 0

a.__contains__("ovo")

import operator
operator.contains(a, "ovo")
```

#### 79、多个特殊字符替换
``` python
input = "This\nstring has\tsome whitespaces...\r\n"
character_map = {
    ord('\n') : ' ',
    ord('\t') : ' ',
    ord('\r') : None
}
# 或者str.maketrans() 生成一个字符一一映射的table

# maketrans第三个参数z字符串中的每个字符都会被映射为None

character_map = str.maketrans("\n\t\r", "   ", "\r")
input.translate(character_map)
```

#### 80、itertools
``` python
import itertools
# islice返回序列seq的从start开始到stop结束的步长为step的元素的迭代器

itertools.islice('ABCDEF', 1, 6, 3)  -> ['B', 'E']
# filterfalse过滤掉predicate为False的元素

itertools.filterfalse(lambda x: x < 5, [1, 4, 6, 4, 1])  -> 6
# takewhile当predicate为False时停止迭代

itertools.takewhile(lambda x: x < 5, [1, 4, 6, 4, 1])   -> 1, 4
# dropwhile当predicate为False时开始迭代

itertools.dropwhile(lambda x: x < 5, [1, 4, 6, 4, 1])   -> 6, 4, 1

with open('/etc/passwd') as f:
    for line in itertools.dropwhile(lambda line: line.startswith('#'), f):
        print(line, end='')
```

#### 81、any / all
``` python
for row in rows:
    if row[0] == 0 and row[1] != 'YES':
        return True
    return False

return any(row[0] == 0 and row[1] != 'YES' for row in rows)

a=np.array([1,2,3])
b=a.copy()
(a == b).all()
```

#### 82、keras class_weight 和 sample_weights
class_weight 影响在目标函数中每个类别的相对权重，主要适用于样本类别不平衡的情形，如异常交易检测。
sample_weights 运行进一步控制同一个类别下样本的相对权重，主要用于样本质量不同的情形，比如点击并购买的样本权重高，只有点击的样本权重低。
class_weight：字典结构，不同的类别映射到不同的权值，训练过程中调整损失函数（仅用于训练）。在处理不平衡的训练数据时，可以使得损失函数对样本数不足的类别更加关注。
sample_weight：numpy array 结构，训练过程调整损失函数（仅用于训练）。可以传递一个1D的与样本等长的向量用于对样本进行1对1的加权。
``` python
from sklearn.utils import class_weight
class_weight = class_weight.compute_class_weight('balanced', np.unique(y_train), y_train)
model.fit(X_train, y_train, class_weight=class_weight)

tf.nn.weighted_cross_entropy_with_logits(
    labels, logits, pos_weight, name=None
)

# 交叉熵损失

loss = -[labels * log(sigmoid(logits)) + (1 - labels) * log(1 - sigmoid(logits))]

# 加权交叉熵损失

loss = -[labels * log(sigmoid(logits)) * pos_weight + (1 - labels) * log(1 - sigmoid(logits))]

```

#### 83、tf损失函数
``` python
# softmax_cross_entropy_with_logits_v2 先 softmax 再求交叉熵。把一个维度上的 labels 作为一个整体判断，结果给出整个维度的损失值，labels中每一维只能包含一个1。多分类问题首选。

# sigmoid_cross_entropy_with_logits 先 sigmoid 再求交叉熵。结果是一组向量，是每个维度单独的交叉熵，如果求总的交叉熵，使用 tf.reduce_sum() 相加即可；如果想求 loss ，则使用 tf.reduce_mean() 进行平均。labels中每一维可以包含多个1。二分类问题首选。

x = tf.constant([7, 6, -4], tf.float64)
x_sig = tf.nn.sigmoid(x)
z = tf.constant([1,1,0], tf.float64)
loss_1 = tf.reduce_mean(z * -tf.log(x_sig) + (1 - z) * -tf.log(1 - x_sig))
loss_2 = tf.reduce_mean(tf.nn.sigmoid_cross_entropy_with_logits(logits=x, labels=z))

logits = tf.constant([[3, -4], [4, -2], [-2, 2]], tf.float64)
y = tf.nn.sigmoid(logits)
y_ = tf.constant([[1, 0], [1, 0], [0, 1]], tf.float64)
loss_4 = -tf.reduce_mean(y_ * tf.log(y))
loss_5 = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits_v2(logits=logits, labels=y_))

```

#### 84、property动态属性
``` python
from datetime import date, datetime
class User:
    def __init__(self, name, birthday):
        self.name = name
        self.birthday = birthday
        self._age = 0
    def get_age(self):
        print("func age")
        return datetime.now().year - self.birthday.year
    # 将用函数取值的模式, 修改为用属性取值
    @property
    def age(self):
        print("attr age")
        return datetime.now().year - self.birthday.year
    @age.setter
    def age(self, value):
        print("age.setter")
        self._age = value


user = User("Tom", date(year = 1998, month = 1, day = 1))
user.age = 20
print(user.age)
print(user._age)

```

#### 85、asyncio
``` python
import asyncio
import aiohttp
import time

async def download_one(session, semaphore, url):
    async with semaphore:
        async with session.get(url) as resp:
            html = await resp.text()
            print('Read {} length {} from {}'.format(html, resp.content_length, url))

async def download_all(sites):
    async with aiohttp.ClientSession() as session:
        # 限制并发量
        semaphore = asyncio.Semaphore(2)
        #tasks = [asyncio.ensure_future(download_one(session, site)) for site in sites]
        tasks = [asyncio.create_task(download_one(session, semaphore, site)) for site in sites]
        await asyncio.gather(*tasks)

def main():
    sites = [
        'http://c.biancheng.net',
        'http://c.biancheng.net/c',
        'http://c.biancheng.net/python'
    ]
    start_time = time.perf_counter()
    loop = asyncio.get_event_loop()
    try:
        loop.run_until_complete(download_all(sites))
    finally:
        loop.close()
    end_time = time.perf_counter()
    print('Download {} sites in {} seconds'.format(len(sites), end_time - start_time))

if __name__ == '__main__':
    main()
```




