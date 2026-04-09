# Selenium

先要`pip install selenium`  
现在的版本已不需要手动装驱动  

## 实例化

```py
from selenium import webdriver

options = webdriver.EdgeOptions()
    
    # 1. 核心伪装参数：移除 "enable-automation" 标志
    options.add_experimental_option("excludeSwitches", ["enable-automation"])
    
    # 2. 核心伪装参数：禁用自动化特征检测
    options.add_argument("--disable-blink-features=AutomationControlled")
    
    # 3. (可选) 模拟真实用户分辨率，防止指纹识别
    # options.add_argument("--window-size=1920,1080")

    driver = webdriver.Edge(options=options)
    
    # 4. 【关键修正】必须在打开网页之前注入 JS！
    # 使用 CDP 命令在文档加载前执行脚本
    driver.execute_cdp_cmd("Page.addScriptToEvaluateOnNewDocument", {
        "source": """
            Object.defineProperty(navigator, 'webdriver', {
                get: () => false
            });
        """
    })

    # 5. 访问页面
    driver.get("https://www.zhihu.com/topic/19591985/unanswered")
```

上述代码比`driver = webdriver.Edge()`要好，更好的伪装（直接粘贴），其中options.add_argument() 此函数可加入更多信息

## driver方法和属性

- driver.get("<https://www.zhihu.com/topic/19591985/unanswered>")  打开网页  
- driver.quit() 关闭浏览器
- driver.forward() 返回下一页
- driver.back() 返回上一页
- driver.close()  关闭当前问题标签页
- driver.execute_script("window.scrollBy(0, 300);") 执行JS命令，这条命令使页面下滑
- driver.refresh() 刷新当前页面
- driver.page_source 获得HTML源码数据
- driver.title 获取标题

---

需要用By模块

- driver.find_element(By.ID, "kw")：返回一个 WebElement 对象。找不到会报错。
- driver.find_elements(By.ID, "kw")（注意有个 s）：返回一个列表（List）。找不到不会报错，而是返回空列表 []

为了保证脚本的稳定性和速度，建议按照以下优先级选择定位方式：
ID > Name > CSS Selector > XPath > 其他方式  

---

- driver.window_handles  获取所有窗口句柄列表。当处理多标签页（如点击链接打开新标签）时切换窗口。
- driver.switch_to.window(handle) 切换到指定窗口。 配合 window_handles 使用，切换操作焦点。
- driver.current_window_handle 获取当前窗口句柄（ID），为了保存主窗口 ID，以便后续切回来。

### 处理

如果是在当前标签页处理的话

```py
driver.forward() 返回下一页
driver.back() 返回上一页
```

如果是新开一个标签页处理的话

```py
main_window = driver.current_window_handle

element.click()  # 点击进入问题详情页
wait.until(lambda d: len(d.window_handles) > 1) # 当窗口数>1
driver.switch_to.window(driver.window_handles[-1])
driver.close()  # 关闭当前问题标签页
driver.switch_to.window(main_window)  # 切回主标签页
```

## wait.until() 函数

它比 time.sleep() 更智能、更高效。  
接收函数：wait.until() 接收一个函数对象（通常是 expected_conditions 模块中的方法），而不是函数的执行结果。  
循环检查：Selenium 会把driver传入函数，每隔一小段时间（默认 0.5 秒）调用一次这个函数。  
返回结果：一旦函数返回 非 False/非 None 的值（例如找到了元素），等待立即结束并返回该结果。  
超时异常：如果在指定时间内条件一直不满足，抛出 TimeoutException。  

### EC的方法

所有方法里的定位器都是两层括号，例如 ((By.ID, "kw"))。这是因为外层括号是函数的参数，内层括号是一个元组 (By.ID, "kw")。千万不要少写一层括号。参数用到By模块  

| 方法名 | 含义 | 适用场景 |
| :--- | :--- | :--- |
| `presence_of_element_located` | 元素在 DOM 中 | 元素已经存在于页面源代码中，但不一定看得到（比如隐藏域）。 |
| `visibility_of_element_located` | 元素可见 | 元素不仅存在，而且在页面上看得到（宽高不为 0）。 |
| `element_to_be_clickable` | 元素可点击 | 元素可见且未被禁用。（点击按钮前最推荐用这个） |
| `invisibility_of_element_located` | 元素不可见 | 等待某个元素消失（比如等待“加载中...”的提示语消失）。 |

```py
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By

wait = WebDriverWait(driver, 15)

element = wait.until(EC.presence_of_element_located((By.XPATH, question_xpath)))

element.click()  # 点击进入问题详情页
wait.until(EC.presence_of_element_located((By.TAG_NAME, "body")))  # 等待页面加载完成
element.send_keys("手机") # 传入
```

## By模块

| 定位策略 | 语法示例 | 适用场景与说明 |
| :--- | :--- | :--- |
| ID 定位 (推荐) | `By.ID, "kw"` | 最快、最准。利用元素的 `id` 属性，通常 id 是唯一的。 |
| Name 定位 | `By.NAME, "wd"` | 常用于表单输入框（如搜索框、密码框），利用 `name` 属性。 |
| XPath 定位 (万能) | `By.XPATH, "//div[@id='app']"` | 最灵活。可以定位任何元素，支持层级查找、文本查找，但速度稍慢。 |
| CSS 选择器 (推荐) | `By.CSS_SELECTOR, ".btn"` | 速度快。语法简洁，类似写 CSS 样式，适合复杂结构的定位。 |
| 链接文本 | `By.LINK_TEXT, "登录"` | 专门用于 `<a>` 标签，必须完全匹配链接上的文字。 |
| 部分链接文本 | `By.PARTIAL_LINK_TEXT, "登"` | 专门用于 `<a>` 标签，模糊匹配文字（只要包含“登”即可）。 |
| 类名 | `By.CLASS_NAME, "s_ipt"` | 利用 `class` 属性。注意：如果元素有多个 class，只能选其中一个，不能有空格。 |
| 标签名 | `By.TAG_NAME, "input"` | 利用 HTML 标签名（如 `div`, `a`, `input`）。通常用于获取一组元素。 |
