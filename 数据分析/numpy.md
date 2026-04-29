# NumPy 学习笔记

```python
import numpy as np
```

NumPy 是 Python 科学计算的核心库，擅长处理**向量（1D）**和**矩阵（2D）**。  
它和 list 类似，但更适合数值计算，支持广播、矩阵运算和高效统计。

---

## 一、基础概念与核心特点

### 数组与维度

- 一维数组：通常表示向量
- 二维数组：通常表示矩阵

---

- `arr.shape`：查看维度
- `arr.reshape((6,4))`：重塑维度
- `np.newaxis`：在索引位置插入一个长度为 1 的维度。它本质上等价于 None。

```py
import numpy as np

# 专门为广播对齐形状,与reshape开课互用
a = np.array([1, 2, 3])   # shape: (3,)
a_col = a[:, np.newaxis]  # shape: (3, 1) 列向量
a_row = a[np.newaxis, :]  # shape: (1, 3) 行向量
```

---
`axis = 1`表示第一维  
`axis = 0`表示第二维  
如果是3维数组，会有`axis = 2`表示第3维

### dtype（同类型）

NumPy 数组元素类型统一。  
数据一般是整型和浮点型  
混合类型时会自动升级；  
根据`str > float > int > bool`进行转换；  
如果不是上述类型数据，变成 `object`；  
float放入整型，自动截断  
整型放入浮点型，自动升级  
在运算过程中整型可能会自动转换
**使用astype可以转换类型**

```python
np.array([1, '2', 3])         # '1' '2' '3'
np.array([1, '2', 3, None])   # 为 object

arr1 = np.array([1,2,3])
arr1[1] = 10.9 # arr[1] = 10 float放入整型，自动截断

arr2 = arr1.astype(float) # 使用astype可以转换类型
arr1 = arr1.astype(float)
```

### 长度固定

数组创建后长度不可直接增删（不像 list 的 del/pop，会报错）。  
`np.append` 返回新数组，原数组不变。

```py
result = np.append(arr,[2,11,22]) # arr 没变,list是追加
print(result)
```

### 切片和赋值为视图（view）

切片通常共享数据，改切片影响原数组。  
需要独立副本时用 `.copy()`。
赋值依然没创建新的变量

---

## 二、创建数组（1D / 2D）

### 一维创建

```python
a = np.array([1, 2, 3, 4])

b = np.arange(1, 10, 2)        # [1 3 5 7 9]
c = 1.0 * np.arange(1, 10, 2)  # 1. 3. 5. 7. 9.

d = np.ones(3)                 # [1. 1. 1.]
e = np.zeros(3)                # [0. 0. 0.]
f = np.ones(3) * 3.14          # 常数数组
```

### 二维创建

```python
data = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]
arr = np.array(data)

arr2 = np.arange(24).reshape(6, 4) # list和arange + reshape  
arr3 = np.arange(24).reshape(6, -1)  # -1让它自己算  
flat = arr2.reshape(-1)    # -1让它自己算 把二维降级为一维数组  
arr1 = np.ones((1,3))                # 全是1.0 
```

---

## 三、索引、切片、筛选

### 索引取值和修改

```python
arr = np.array([1, 2, 3, 4])
arr[1]        # 2
arr[-1]       # 4
arr[[0, 2]]   # [1 3]

arr[3] = 5
```

```python
mat = np.array([
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
])

mat[1, 2]      # 2行3列
mat[2,-2]      
mat[0, :]      # 第1行
mat[:, 0]      # 第1列
mat[0:2, 0:2]  # 取前两行前两列
mat[[0,1],[0,0]] # 取特定的元素

arr[1,1] = 100
arr[[0,1],[0,0]] = 100 # 修改特定元素
```

### 切片是视图

```python
arr = np.array([10, 20, 30, 40])
cut = arr[0:2]
cut[0] = 999
print(arr)  # 原数组也变化

safe = arr[0:2].copy()
safe[0] = -1
print(arr)  # 原数组不受 safe 影响
```

### 布尔筛选与逻辑运算

- 比较会得到布尔数组
- 逻辑组合用 `& | ~`（不要直接用 and/or/not）

```python
arr = np.array([1, 2, 3, 10, 20, 30])
arr1 = arr[[False,False,False,True,True,True]]
# 从arr中筛选出为True的值

arr1 = arr > 3
print(arr1) # [False False False  True  True  True]

arr1 = arr[arr > 3] # [10 20 30]
# 矩阵会退化为向量

arr[(arr > 3) & (arr < 30)]    # [10 20]
arr[(arr > 3) | (arr < 3)]     # [1 2 10 20 30]
arr[~(arr < 30)]               # [30]
```

---

- `np.sum(np.abs(arr)<1,)`:统计True
- `np.any(cond,axis=1)`：至少一个 True
- `np.all(cond,axis=1)`：全部 True  
**上面可以带上axis参数进行二维的统计**

- `np.where(...)`：条件索引/条件赋值(三元表达式)
- `np.argmax()`：“最大值在拉平成一维后排第几个”  
- `np.unravel_index()`：再把这个“一维编号”还原成“多维坐标”

```python
arr1 = np.arange(1,6)
arr2 = np.arange(6,1,-1)

arr1 > arr2 会生bool 数组

arr = np.array([100, 700, 680, 20])

np.any(arr > 650)          # True
np.all(arr > 0)            # True

idx = np.where(arr > 650)  # 索引数组组成的元组（tuple）
arr[idx]                   # [700 680]

np.where(arr > 650, 1, 0)  # 条件赋值
# 如果数组 arr 中的元素大于 650，就将其变为 1，否则变为 0

# 条件为真时填 1，条件为假时填 arr (原数组)
result = np.where(arr > 650, 1, arr)

# 条件为真时填 arr * 2，条件为假时填 0
result = np.where(arr > 650, arr * 2, 0)

np.where(arr == np.max(arr)) # 找最大值

# 找最大值最优解
flat_idx = np.argmax(arr)   # 1
row, col = np.unravel_index(idx, arr.shape)
```

---

## 四、运算与广播

### 逐元素运算

同维度矩阵和矩阵/数组和数组都是对应元素进行操作
`+ - * / // ** %` 都是逐元素计算。  

向量与矩阵，向量自动变成行矩阵
行矩阵/列矩阵会广播以适配另外一个矩阵形状
如果是行矩阵与列矩阵同时运算，会同时广播
向量会根据需求充当行/列矩阵

```python
a = np.array([1, 2, 3, 4])
b = np.array([2, 3, 4, 5])

a + b
a - b
a * b
a / b
```

---

```py
list1 = [1,2,3,4]
list2 = [2,3,4,5]
print(list1 + list2)
# [1, 2, 3, 4, 2, 3, 4, 5]

arr1 = np.array([1,2,3,4])
arr2 = np.array([2,3,4,5])
print(arr1 + arr2)
# [3 5 7 9]


data = [
    [1,2,3],
    [3,1,5]
]

arr = np.array(data)
print(arr + arr) # 按位运算
```

### 广播机制

形状不完全一样时，只要兼容就能自动扩展。  

#### 兼容条件

1. 维度对齐
如果两个数组的维度数量不同，NumPy 会在维度较少的数组形状的前面补 1，直到两个数组的维度数量相同。
例如：一个形状为 (4, 3) 的二维数组和一个形状为 (3,) 的一维数组运算时，(3,) 会被视为 (1, 3)

2. 从后向前比较
从最后一个维度（最右边）开始，向前逐一比较两个数组在每个维度上的大小。

3. 兼容性规则
对于每一对维度，只要满足以下任意一个条件，即为兼容：
两个维度的长度相等。
其中一个维度的长度为 1。

只有当所有维度都兼容时，广播才能成功。如果任何一个维度不兼容，NumPy 就会抛出 ValueError 异常。

---
广播示例:

- 数组 A (2, 3) 与 数组 B (3,)  
对齐: B 的形状 (3,) 补 1 变为 (1, 3)。
比较:  
最后一维：A 是 3，B 是 3 → 相等，兼容。  
倒数第二维：A 是 2，B 是 1 → 其中一个为1，兼容。  
结果: 广播成功，运算结果的形状为 (2, 3)。  

- 数组 A (3, 1) 与 数组 B (1, 3)  
对齐: 维度数量相同，无需补 1。  
比较:  
最后一维：A 是 1，B 是 3 → 其中一个为1，兼容。  
倒数第二维：A 是 3，B 是 1 → 其中一个为1，兼容。  
结果: 广播成功，运算结果的形状为 (3, 3)。  

- ❌ 无法广播的示例  
数组 A (2, 3) 与 数组 B (3, 2)  
对齐: 维度数量相同，无需补 1。  
比较:  
最后一维：A 是 3，B 是 2 → 不相等，且都不是1，不兼容。  
倒数第二维：A 是 2，B 是 3 → 不相等，且都不是1，不兼容。  
结果: 广播失败。  

---

| 数组 A 形状 | 数组 B 形状 | 能否广播 | 原因 |
| :--- | :--- | :--- | :--- |
| `(3,)` | `()` (标量) | ✅ 是 | 标量可广播至任意形状。 |
| `(2, 3)` | `(3,)` | ✅ 是 | B 被视为 `(1, 3)`，所有维度兼容。 |
| `(1, 3)` | `(2, 1)` | ✅ 是 | 所有维度均满足“其中一个为1”的条件。 |
| `(2, 3)` | `(3, 2)` | ❌ 否 | 所有维度均不兼容。 |

```python
a = np.array([1, 2, 3, 4])
a + 1   # [2 3 4 5]
```

二维广播示例：

```python
mat = np.array([
    [1, 2, 3],
    [3, 1, 5]
])

mat + np.array([1, 2, 3])     # 按行广播
mat + np.array([[1], [2]])    # 按列广播
```

```py
import numpy as np

# --- ✅ 示例 1: (2, 3) 与 (3,) ---
A1 = np.array([[1, 2, 3], [4, 5, 6]]) # 形状 (2, 3)
B1 = np.array([10, 20, 30])          # 形状 (3,)
print(f"示例1结果形状: { (A1 + B1).shape }") # 输出: (2, 3)

# --- ✅ 示例 2: (3, 1) 与 (1, 3) ---
A2 = np.array([[1], [2], [3]])       # 形状 (3, 1)
B2 = np.array([[10, 20, 30]])        # 形状 (1, 3)
print(f"示例2结果形状: { (A2 + B2).shape }") # 输出: (3, 3)

# --- ❌ 示例 3: (2, 3) 与 (3, 2) ---
A3 = np.array([[1, 2, 3], [4, 5, 6]]) # 形状 (2, 3)
B3 = np.array([[10, 20], [30, 40], [50, 60]]) # 形状 (3, 2)
try:
    print((A3 + B3).shape)
except ValueError as e:
    print(f"示例3报错: {e}")
```

---

## 五、线性代数（矩阵乘法）

`*` 是逐元素乘法；  
矩阵乘法请用 `np.dot(A, B)` 或 `A @ B`。  
混有向量时，结果必为向量  

```python
v1 = np.arange(5)
v2 = np.arange(5)
np.dot(v1, v2)   # 是(1,5) * (5,1)  
# (5,) *(5,3) = (1,5)* (5,3) = (1,3)
# (3,5) *(5,) = (3,5)* (5,1)
# 否则不为向量
```

```python
A = np.arange(15).reshape(3, 5)
B = np.arange(10).reshape(5, 2)
A @ B            # 形状 (3, 2)
```

---

## 六、常用函数汇总（高频）

### 统计与聚合

- `np.sum(arr)` / `arr.sum()`
- `np.mean(arr)` / `arr.mean()`
- `np.max(arr)` / `arr.max()`
- `np.min(arr)` / `arr.min()`
- `np.median(arr)`

```python
mat = np.array([[1, 2, 3], [3, 1, 5]])

np.sum(mat)          # 15
np.sum(mat, axis=0)  # [4 3 8]
np.sum(mat, axis=1)  # [6 9]

np.mean(mat, axis=1)
np.max(mat, axis=0)
```

### 缺失值安全统计（nan 系列）

数据中有 `np.nan` 时，优先用 `nan*` 版本。

```python
arr = np.array([1.0, 2.0, np.nan, 4.0])

np.sum(arr)      # nan
np.nansum(arr)   # 7.0

np.mean(arr)     # nan
np.nanmean(arr)  # 2.333...
```

### 数学函数

- `np.abs`、`np.round`
- `np.sin`、`np.cos`、`np.tan`
- `np.exp`、`np.log`、`np.log2`

```python
np.abs([-1, -3, 2]) # 绝对值
np.round([1.2244, 2.324], 2) # 小数
np.sin(arr)
np.cos(arr)
np.tan(arr)

x = np.array([1, 2, 3])
np.exp(x) # e^x 
np.log(x) # ln(x) 
np.log(x)/np.log(2) # log2(x)
2**arr # 2^x
```

### 随机数

`rng = np.random.default_rng(42)`

```python
rng.integers(1, 100, size=10)
rng.integers(1, 100, size=(1, 15))

rng.random(size=5)
rng.random(size=(2, 5))
rng.random(size=(3, 3)) * 40 + 60  # [40,60)

rng.normal(loc=0, scale=1, size=(2, 3))
loc 是均值，scale 是标准差，不写参数名直接写 0, 1 也可以
```

### 结构变换与拼接分割

- `arr.T`：转置，用于二维矩阵(求转置)，一维转置还是自己/也可用reshape
- `np.flipud` / `np.fliplr`：上下 / 左右翻转(数组只能用前者(数学里矩阵是竖着的))
- `np.concatenate()`：拼接
- `np.split()`：分割

```python
np.concatenate([arr1,arr2]) 把2个数组拼接

arr1 = np.array([[1,2,3],[4,5,6]])
arr2 = np.array([[7,8,9],[10,11,12]])

np.concatenate([arr1, arr2], axis=0)  # 纵向
np.concatenate([arr1, arr2], axis=1)  # 横向
```

---

```py

np.flipud(arr1)
np.fliplr(arr1)

arr = np.arange(0, 100, 10)
a, b, c = np.split(arr, [2, 8])

arr1,arr2 = np.split(arr,[1],axis = 0) 
arr1,arr2,arr3 = np.split(arr,[1,3],axis = 1)

```

---
