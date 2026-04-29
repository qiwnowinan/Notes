# pandas

擅长处理和清理数据  
先要`import pandas as pd`  
pd中，numpy数组的特征仍在，pd的数据以numpy数据方式存储  

## Series

### 创建

创建数据后，左侧为数据标签（索引）。如果不指定，默认是从 0 开始的整数索引(不代表默认索引不能用了)  

- 用list  
`print(pd.Series([40,29,35,40,60]))`  
会得到如下结果：  

```py
0    40  
1    29  
2    35  
3    40  
4    60  
dtype: int64  
```

`print(pd.Series([40,29,35,40,60],index = ['张三','李四','王五','赵六','王翠花']))`  

会得到如下数据：

```py
张三     40
李四     29
王五     35
赵六     40
王翠花    60
dtype: int64
```

- 用dict  

```py
data = {'张三' : 40,
        '李四' : 29,
        '王五' : 35,
        '赵六' : 40,
        '王翠花' : 60
}
pd.Series(data) # 会得到上述的数据  
```

- 修改索引和属性

```py
se = pd.Series([40,29,35,40,60],index = ['张三','李四','王五','赵六','王翠花'])

print(se.index) # 获得index  输出：# Index(['张三', '李四', '王五', '赵六', '王翠花'], dtype='object')  是一个Index对象

print(se.index.tolist()) # 把index转成list,方便进行操作

# 通过直接对se.index 赋值改变索引
se.index = ['1','2','3','4','5']

print(se.values) # 获得values,输出：[40 29 35 40 60]
```

### 取值

#### 索引

```py
se = pd.Series([40,29,35,40,60],index = ['张三','李四','王五','赵六','王翠花'])

# 注意里面只有一个参数
print(se.iloc[0])  # 输出：40
print(se.loc["张三"])  # 输出：40 (通过标签访问)

print(se.loc[["张三", "王五", "李四"]]) # 不连续取值
```

#### 切片

```py
print(se.iloc[0:3]) # 不包括[3]
print(se.loc["张三":"赵六"]) # 包括“赵六”
```

---

## DataFrame

### 创建

- list

```py
data = [['张三','男','唱'],
        ['李四','女','跳']]

df = pd.DataFrame(data,index=['001','002'],columns=['姓名','性别','爱好'])
# 如果没有index和columns参数会有默认参数

'''
     姓名 性别 爱好
001  张三  男  唱
002  李四  女  跳
'''
```

- dict(最常用)

```py
data = {
        '姓名':['张三','李四'],
        '性别':['男','女'],
        '爱好':['唱','跳']
}

df = pd.DataFrame(data,index=['001','002'])
# 默认把key当成列索引
# 如果没有index参数，会有默认的
```

- 修改索引和属性

```py
df.index = ['01','02']
df.columns = ['name','gender','hobby']

print(df.index.tolist())
print(df.columns.tolist())

print(df.values) 
# 输出二维 numpy 数组，例如：
# [['张三' '男' '唱']
#  ['李四' '女' '跳']]

# 当想把DataFrame的数据转成numpy数组进行操作时，可以使用values属性，得到一个二维的numpy数组，行表示数据的行，列表示数据的列
```

### 取值

#### 索引

```py
print(df["性别"]) # 取一列
print(df[["性别", "爱好"]]) #取多列，[]里面是一个参数
```

#### 切片

```py
r = df[0:2] # 是行切片(取前两行)
r['姓名']

df[0:2]['姓名']
```

#### 筛选取行

`r = df[df["姓名"] == "张三"]`

#### iloc和loc(推荐)

```py
df.loc["001", "爱好"] = "跳" # 修改/找单个元素
df.loc["002"] # 取行(loc默认取行)
r = df.loc[:, "姓名"] # 取列
df.loc["001":"002", "姓名":"性别"]
df.loc[["001", "002"], ["姓名", "性别"]]

df.iloc[0, 2]  # 修改/找单个元素
df.iloc[1]  # 取行
df.iloc[:, 0]  # 取列
df.iloc[0:2, 0:2]
df.iloc[[0, 1], [0, 1]]
```

---

## 数据清洗

### 读取数据

```py
df = pd.read_csv("./notifier_title_date_link.csv",encoding='utf-8-sig')  # 读取csv文件，encoding参数指定编码格式，utf-8-sig可以解决中文乱码问题
print(df)

df1 = pd.read_excel("./notifier_title_date_link.xlsx", sheet_name="Sheet1")
# excel可以有多个Sheet，默认是第一个（sheet_name=0），可以通过sheet_name参数指定sheet
```

### 数据变形和操作

```py
df = df.T  # 转置，行列互换

df = df.iloc[:, ::-1]  # 列反转，行不变
df = df.iloc[::-1, :]  # 行反转，列不变

df['牌照'] = sr3  # 新增一列，牌照列的值是sr3这个Series的值，sr3的索引要和df的索引一致
df.loc['001'] = sr3  # 新增一行，索引是001，值是sr3这个Series的值，sr3的索引要和df的列索引一致

sr4 = df['牌照']  # 取出牌照列，得到一个Series，索引是df的索引，值是牌照列的值

pd.concat([sr1, sr2], axis=0)  # 连接两个Series/DataFrame，axis=0表示按行连接，axis=1表示按列连接，得到一个新的Series/DataFrame，索引是两个Series/DataFrame的索引的并集，值是两个Series/DataFrame的值

sr = sr + 10  # Series的每个元素都加10，得到一个新的Series，索引不变，值是原来值加10的结果
df['好评数'] = df['好评数'] + 1000  # DataFrame的每个元素都加1000，得到一个新的DataFrame，索引不变，值是原来值加1000的结果

sr1 - sr2  # Series的每个元素都减去sr2对应索引的值，得到一个新的Series，索引不变，值是原来值减去sr2对应索引的值的结果(如果sr2中没有对应索引的值，则结果为NaN)

np.sqrt(sr)  # Series的每个元素都开平方，可以直接用numpy的函数，得到一个新的Series，索引不变，值是原来值开平方的结果

print(df.head(3))  # 看前三行数据
print(df.info())  # 查看数据的基本信息

sort_df = df.sort_values(by="date", ascending=False)  # 按照date列降序排序, ascending=True是升序
print(sort_df)

df[df["好评数"] > 40000]  # 筛选出date列大于2024-01-01的数据

# 但如果要筛选“多个特定值”（例如：只要“北京”和“上海”的数据），用 | (或) 连接会很麻烦
# 假设我们要筛选出 城市 为 "北京" 或 "上海" 的数据
cities = ['北京', '上海']
# 语法：df[df['列名'].isin(列表)]
result = df[df['城市'].isin(cities)]


df["好评数"].mean()  # 求好评数的平均值

df['总评数'] = df['好评数'] + df['差评数']  # 新增一列，总评数是好评数和差评数的和

df.describe()  # 查看数值型数据的统计信息，包括count、mean、std、min、25%、50%、75%、max等统计量
```

### 存储数据

```py
df.to_csv("./notifier_title_date_link.csv",encoding='utf-8-sig',header=True, index=False)  # 存储为csv文件，header=True表示保留列名，index=False表示不保存行索引
df.to_excel("./notifier_title_date_link.xlsx", index=False)  # 存储为excel文件,通常不需要 encoding 参数
```

### 处理空值

```py
sr.isnull()  # 判断每个元素是否是空值，返回一个布尔值的Series
sr[sr.isnull()]  # 查看空值的元素

data.drop(["总评数"], axis=1, inplace=True)  # 删除总评数列，axis=1表示删除列，inplace=True表示在原数据上修改
data.dropna(inplace=True)  # 删除有空值的行
data.fillna(0, inplace=True)  # 用0填充空值

data.ffill(axis=0, inplace=True)  # 向前填充,axis=0表示按行填充,axis=1表示按列填充
data.bfill(axis=0, inplace=True)  # 向后填充
```

### 重复数据

```py
data.duplicated()  # 判断每行是否重复，返回一个布尔值的Series
data[data.duplicated()]  # 查看重复的行
data.drop_duplicates(inplace=True)  # 删除重复行
```

### 数据转换

```py
def split_salary(salary):
    pass

data['平均薪资'] = data['薪水'].apply(split_salary)  # 对薪水列应用split_salary函数，得到平均薪资列
# apply方法可以对Series中的每个元素应用一个函数，返回一个新的Series
```

---

## 数据分析

### 统计

```py
df = pd.DataFrame({
    "城市": ["北京", "上海", "北京", "广州", "上海", "北京", None],
    "学历": ["本科", "硕士", "本科", "本科", "硕士", "博士", "本科"]
})

# 统计“城市”每个值出现次数（默认降序，默认不统计 NaN）
print(df["城市"].value_counts())
# 北京    3
# 上海    2
# 广州    1

# 把 NaN 也统计进去
print(df["城市"].value_counts(dropna=False))
# 北京     3
# 上海     2
# 广州     1
# NaN      1

# 归一化（占比）
print(df["城市"].value_counts(normalize=True))
# 北京    0.50
# 上海    0.33
# 广州    0.17
```

### 分组

```py
data["平均薪资"].mean()  # 求平均薪资的平均值
data["平均薪资"].max()  # 求平均薪资的最大值

data[data['城市'] == '北京']['平均薪资'].mean()  # 求北京的平均薪资的平均值

# groupby方法可以按照某个列进行分组，得到一个GroupBy对象，可以对每个组进行操作
group = data.groupby("城市")  # 按照城市分组

group.size()  # 查看每个组的大小

group["平均薪资"].mean()  # 求每个组的平均薪资的平均值

group.get_group("北京")  # 获取北京组的数据

for name, group in group: # 遍历每个组，name是组名，group是组的数据
    print(name)  # 打印组名
    print(group)  # 打印组的数据
```

### 聚合

```py
data.groupby("城市")["平均薪资"].median()  # 对每个组的平均薪资进行聚合，求中位数
data.groupby("年限要求")["平均薪资"].agg(["mean", "median"])  # 对每个组的平均薪资进行聚合，求平均值、中位数
# agg方法可以对每个组进行多个聚合操作，参数是一个列表，列表中的元素是要进行的聚合操作，函数以字符串的形式传入

def difference(x):
    return x.max() - x.min()

data.groupby("年限要求")["平均薪资"].agg(difference)  # 对每个组的平均薪资进行聚合，求最大值和最小值的差值
# 也可以自定义一个函数，传入agg方法进行聚合操作，函数的参数是一个Series，返回一个值
# 自定义函数以函数对象的形式传入agg方法
```

### 时间序列转换

```py
data["date"] = pd.to_datetime(data["date"])  # 将date列转换为datetime类型

df = df.set_index("date")  # 将date列设置为索引

df.loc['2014'] # 取出2014年的数据，loc方法可以通过标签进行切片，datetime类型的索引可以通过年份、月份、日期等进行切片

df['day'] = df.index.day  # 从索引中提取出日期，创建一个新的day列

df['weekday'] = df.index.weekday  # 从索引中提取出星期几，创建一个新的weekday列，0表示周一，6表示周日

# resample也是一种分组，可以直接用一些聚合操作
df.resample('ME').mean()  # 按月末重采样（新版本推荐 ME）
df.resample('YE').sum()   # 按年末重采样（新版本推荐 YE）

 # 按月重采样，求每个月的平均值，resample方法可以对时间序列进行重采样，参数是重采样的频率，'ME'表示按月重采样，'D'表示按天重采样，'YE'表示按年重采样

df.resample('YE').sum()  # 按年重采样，求每年的总和

pd.date_range(start='2024-01-01', periods=10, freq='ME')  # 生成一个日期范围，参数是开始日期、结束日期和频率，freq='ME'表示按月生成日期
```

### 透视表

```py
df.pivot_table(index="城市", columns="年限要求", values="平均薪资", aggfunc="mean")  # 创建一个透视表，index参数是行索引，columns参数是列索引，values参数是值，aggfunc参数是聚合函数，默认是mean

# 结果是：
'''
年限要求   1年以下  1-3年  3-5年  5-10年
城市
北京    50000  60000  70000  80000
上海    45000  55000  65000  75000
'''

# 如果要对年龄分析，年龄在0-120不等，可以先把年龄分成几个区间，比如0-18、19-35、36-60、61-120，然后再进行透视表分析
age = pd.cut(df["年龄"], bins=[0, 18, 35, 60, 120], labels=["0-18", "19-35", "36-60", "61-120"])  # 把年龄分成几个区间，bins参数是区间的边界，labels参数是区间的标签


df.pivot_table(index=[age, '城市'], columns="年限要求", values="平均薪资", aggfunc="mean")  # 如果要对年龄和城市进行分析，可以把年龄和城市都放在index参数中，得到一个多层索引的透视表

# 结果是：
'''
年限要求           1年以下  1-3年  3-5年  5-10年
年龄    城市
0-18    北京    50000  60000  70000  80000
        上海    45000  55000  65000  75000
19-35   北京    55000  65000  75000  85000
        上海    50000  60000  70000  80000
36-60   北京    60000  70000  80000  90000
        上海    55000  65000  75000  85000
'''
```

---

## 高频补充（建议加上）

### isin / query

```py
df[df["城市"].isin(["北京", "上海"])]
df.query("城市 == '北京' and 平均薪资 > 50000")
```

### merge（表连接）

```py
# 内连接
pd.merge(df1, df2, on="用户ID", how="inner")

# 左连接
pd.merge(df1, df2, on="用户ID", how="left")
```

### rename（重命名）

```py
df.rename(columns={"平均薪资": "avg_salary"}, inplace=True)
df.rename(index={"001": "u001"}, inplace=True)
```
