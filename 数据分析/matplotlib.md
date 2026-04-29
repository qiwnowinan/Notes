# matplotlib

先`pip install matplotlib`安装matplotlib库  
后`import matplotlib.pyplot as plt`导入matplotlib库的pyplot模块，并命名为plt  

## 显示中文

### 全局设置中文字体和解决负号显示问题

```python
import matplotlib.pyplot as plt
plt.rcParams['font.sans-serif'] = ['SimHei']  # 设置中文字体为SimHei
plt.rcParams['axes.unicode_minus'] = False  # 解决负号显示问题
```

## 一些常用的全局设置

```py
plt.rcParams['figure.figsize'] = (10, 6)  # 设置图表大小为10x6英寸
plt.rcParams['figure.dpi'] = 100  # 设置图表分辨率为100 dpi
```

## 折线图

```py
fig, ax = plt.subplots(figsize=(8, 4)) # 创建一个图表和一个坐标轴对象，figsize参数设置图表大小为8x4英寸(这是局部设置)，ax是坐标轴对象，可以用来绘制图表

df = df.set_index("date") # 将date列设置为索引
date_data = df.resample('ME').sum() 

ax.plot(x=date_data.index,y=date_data,color='blue',marker='o') # 绘制折线图，date_data.index是x轴数据，date_data是y轴数据，color参数设置线条颜色为蓝色，marker参数设置数据点的标记为圆形

ax.plot(linestyle='', marker='o', color='blue') # linestyle=''表示不绘制线条，只显示数据点，marker='o'表示数据点的标记为圆形，color='blue'表示数据点的颜色为蓝色(这是散点图的绘制方式)

y_ticks = ax.get_yticks() # 获取y轴的刻度值(y轴的真实值),如580000，600000，620000，640000，660000
y_tick_labels = [f'{int(tick/10000)}万' for tick in y_ticks] # 将刻度值转换为以万为单位的标签，如58万，60万，62万，64万，66万
ax.set_yticklabels(y_tick_labels) # 设置y轴的刻度标签为转换后的标签(y轴显示为58万，60万，62万，64万，66万)

x_ticks = pd.date_range(start='2024-01-01', end='2024-12-31', freq='ME') # 生成一个日期范围
ax.set_xticks(x_ticks, rotation=45) # 设置x轴的刻度位置为生成的日期范围，并将刻度标签旋转45度以避免重叠

# 在每个数据点上添加标签
for x, y in zip(date_data.index, date_data['销量']): # 用zip函数将x轴数据和y轴数据组合成一个迭代器，遍历每个数据点的x和y值
    # 格式化数字：除以1w并转为整数
    label_text = f"{int(y / 10000)}万"
    ax.annotate(
        text=label_text,       # 要显示的文字
        xy=(x, y),             # 绑定的真实数据点位置
        xytext=(0, 8),         # 向上偏移 8 个像素（完美错开折线上的圆点）
        textcoords="offset points", # 告诉系统上面的 (0, 8) 是像素偏移
        ha='center',           # 水平居中对齐
        va='bottom',           # 垂直底部对齐
        fontsize=11,           # 字体大小
        color='darkblue'       # 字体颜色
    )
```

## 柱状图

```py
gendar_data = df.groupby('性别')['销量'].sum() # 按性别分组并计算销量总和

fig, ax = plt.subplots(figsize=(8, 4)) # 创建一个图表和一个坐标轴对象，figsize参数设置图表大小为8x4英寸(这是局部设置)，ax是坐标轴对象，可以用来绘制图表

bars = ax.bar(x=gendar_data.index, height=gendar_data,width=0.5,color=['blue','pink']) # 绘制柱状图，gendar_data.index是x轴数据，gendar_data是y轴数据，width参数设置柱子的宽度为0.5，color参数设置柱子的颜色为蓝色和粉色

ax.set_title('不同性别的销量总和', fontsize=14) # 设置图表标题为“不同性别的销量总和”,fontsize参数设置标题字体大小为14
ax.set_xlabel('性别', fontsize=12) # 设置x轴标签为“性别”,fontsize参数设置标签字体大小为12
ax.set_ylabel('销量总和', fontsize=12) # 设置y轴标签为“销量总和”,fontsize参数设置标签字体大小为12

# 在每个柱子上添加标签
# 准备想显示的文字内容（转成'万'为单位）
labels = [f'{int(val/10000)}万' for val in gender_data.values]

# padding=3 表示文字距离柱子顶部 3 个像素
ax.bar_label(bars, labels=labels, padding=3, fontsize=12)
```

## 饼图

```py
pay_methods = ['微信支付', '支付宝', '信用卡', '现金']
sales = [4500, 3500, 1500, 500]

fig, ax = plt.subplots(figsize=(6, 6))

ax.pie(x=sales,             # 饼图的数值数据
       labels=pay_methods,  # 饼图的标签
       autopct='%1.1f%%',   # 自动计算百分比
       startangle=90,       # 旋转角度，默认情况下，饼图的第一块是从时钟的 3 点钟方向开始逆时针画的，视觉上很别扭。
       # 加上 startangle=90，第一块就会从正上方（12点钟方向）开始画，这符合人类阅读习惯。
       colors=['#52C41A', '#1890FF', '#FADB14', '#F5222D'], # 自定义颜色，这是使用16进制颜色代码，分别对应绿色、蓝色、黄色和红色
      )

ax.set_title("各支付方式交易额占比", fontsize=15)
plt.show()
```

## 水平柱状图

```py
# Matplotlib 的 barh 是从下往上一层层画的。所以我们传入数据时特意用了升序（最小的在最前面），这样画出来的水平柱状图就是从上到下递减的，视觉上更符合我们的习惯。
city_data_top = city_data.sort_values(ascending=False)[:10]
city_data_top = city_data_top.sort_values(ascending=True) # 对前10个城市的数据进行升序排序，确保水平柱状图从上到下递减

fig, ax = plt.subplots(figsize=(8, 6)) 
ax.barh(y=city_data_top.index,width=city_data_top.values,color='skyblue') # 绘制水平柱状图，y参数设置y轴数据，width参数设置柱子的宽度，color参数设置柱子的颜色为天蓝色
```

## 直方图

```py
fig, ax = plt.subplots(figsize=(8, 4))

count = df.groupby('ID').size() # 按ID分组并计算每个ID的数量，得到一个Series对象，索引是ID，值是每个ID的数量

distance = 10 # 设置直方图的宽度
group_num = int((count.max()-count.min()) / distance) + 1 # 计算分组的数量，max(count)是最大值，min(count)是最小值，distance是每个组的宽度，+1是为了包含最大值所在的组

ax.hist(count, bins=group_num, color='lightcoral') 

ax.set_xticks(range(0,300,10)) # 设置x轴的刻度位置为0到300，步长为10
```

## 散点图

```py
ax.scatter(x=df['平均薪资'], y=df['年限要求'], color='purple', alpha=0.6) # 绘制散点图，x参数设置x轴数据，y参数设置y轴数据，color参数设置点的颜色为紫色，alpha参数设置点的透明度为0.6
```

## 保存

```py
fig.savefig('chart.png', dpi=300, bbox_inches='tight') # 保存图表

fig.savefig('chart.png', dpi=300, bbox_inches='tight', transparent=True) # 透明背景保存图表

# 矢量图是数学公式渲染的，无限放大不失真
fig.savefig('chart.svg', bbox_inches='tight') # SVG 是一种基于 XML 的矢量图格式，适合保存线条清晰、颜色单一的图表，文件体积较小，适合在网页上使用
```
