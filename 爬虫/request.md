# request

需要先`pip install request`

先看DevTool的常规的请求方法，决定是post or get  

## get

```py
import requests
url = "https://movie.douban.com/top250"
headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/145.0.0.0 Safari/537.36 Edg/145.0.0.0"
}
# params表示在URL后面的参数
params = {
            "start": i,
            "filter": "",
        }
response = requests.get(url, headers=headers, params=params, timeout=10) # timeout 防止超时
```

## post

```py
datas : dict = {"programId": row["programCode"], "type": "org"}
response = requests.post(url, headers=headers, json=datas)
# 如果requests headers里的content-type是json,必须把负载里的参数传给json参数

# 其余时用data参数
# 不要同时传 json 和 data，requests 库会以 json 为准，data 会被忽略。
```

## Session对象

```py

session = request.Session()

# 能自动记下Cookie，其余正常用

response = session.get(url, headers=headers, params=params, timeout=10)

```

## response的方法

```py
user_data = response.json() # 当返回结果为json时，将json字符串自动转换为 Python 字典

html_text = response.text # 获取HTML源码页面

with open(pdf_name, "wb") as f: # 因为是二进制，所以是wb
    f.write(response.content) # 返回原始的二进制数据。保存图片、视频、PDF 文件时必须用这个。
print(f"{pdf_name} 已成功下载")

response.encoding = 'utf-8' # 如果乱码

if response.status_code == 200:
    pass # 判断是否成功


