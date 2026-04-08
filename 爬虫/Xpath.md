# Xpath

当获得HTML源码时，需要数据解析

需要先`pip install lxml`

```py
from lxml import etree
response.encoding = 'utf-8' # 如果乱码
tree = etree.HTML(response.text)

tree = etree.parse('./test.html', etree.HTMLParser(encoding='utf-8')) # 如果已下载了
```

## 具体用法

返回值永远是列表，用之前要先判断列表是否为空  
`r = tree.xpath('//a/text()') # 参数是XPath表达式字符串(最好用单引号)`  

### XPath表达式写法

在浏览器按 F12，在 Elements 面板右键元素 -> Copy -> Copy XPath，可以直接生成路径，然后再手动微调。(考虑问AI)  

| 符号/表达式 | 描述 | 示例 |
| :--- | :--- | :--- |
| `/` | 绝对路径，从根节点开始选取 | `/html/body/div` |
| `//` | 相对路径，从文档任意位置选取（最常用） | `//div` (选所有 div) |
| `.` | 选取当前节点 | `.` |
| `..` | 选取父节点 | `//p/..` (选 p 的父元素) |
| `@` | 选取属性 | `//div/@id` (选 id 属性) |
| `text()` | 获取标签内的文本内容 | `//h2/text()` |
| `[]` | 谓语，用于过滤 | `//div[@class="news"]` |

#### 技巧

索引： //div[1] (选第一个 div)，//div[last()] (选最后一个)。  
逻辑运算： //div[@id='main' and @class='content']。  
多属性 OR： //div[@id='a' or @id='b']。

##### 模糊查找：contains()

如 class="btn btn-primary-red"，直接匹配很难。  
用 contains 可以只匹配一部分：
`tree.xpath('//div[contains(@class, "btn")]') # 找到所有 class 属性里包含 "btn" 这个词的 div。`

```py
items = tree.xpath('//div[@class="item"]/li')
# 用 xpath() 找到包含数据的父节点列表（比如所有的 <li>）。
for item in items:
    # 注意这里的 .// ，点号代表从当前 item 节点开始找
    title = item.xpath('./h2/text()')[0] 
    # 遍历这个列表，对每个父节点再次使用 xpath() 提取细节。
```

#### 提取

普通提取：`//p/text() # \n   你好 世界   \n"`
清洗提取：`//p/normailze-space() # "你好 世界"`
