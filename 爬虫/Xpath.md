# Xpath

当获得HTML源码时，需要数据解析

需要先`pip install lxml`

```py
from lxml import etree
tree = etree.HTML(response.text)
response.encoding = 'utf-8' # 如果乱码

tree = etree.parse('./test.html', etree.HTMLParser(encoding='utf-8')) # 如果已下载了
```

## 具体用法
