# awesome-Spider-CN

### 微博

### 知乎

### Bilibili

### 百度指数

### Twitter




<br/>

### Requests

- 发起http请求

```python
import requests
# get请求
r = requests.get('www.xxx.com')

# post请求
r = requests.post('www.xxx.com')
```

- 传递参数

```python
# params
payload = {'key1':'value1', 'key2':'value2'}
r = requests.get("www.xxx.com",params = payload)
print(r.url)

# 传递json
import json
url = 'https://api.github.com/some/endpoint'
payload = {'some': 'data'}
r = requests.post(url, data = json.dumps(payload))
```

- 查看响应内容

```python
import requests
r = requests.get('https://api.github.com/events')
r.encoding = 'ISO-8859-1'
# 文本形式
print(r.text)
# 字节形式
print(r.content)
# 二进制创建图片
from PIL import Image
from io import BytesIO
i = Image.open(BytesIO(r.content))
# json形式
print(r.json())
# 原始数据
print(r.raw)
# 查看响应状态码
print(r.status_code)
```

- 请求头相关

```python
# 定制响应头
url = 'www.xxx.com'
headers = {'user-agent': 'my-app/0.0.1'}
r = requests.get(url, headers = headers)

# 查看响应头
r.headers
>>>
{
    'content-encoding': 'gzip',
    'transfer-encoding': 'chunked',
    'connection': 'close',
    'server': 'nginx/1.0.4',
    'x-runtime': '148ms',
    'etag': '"e1ca502697e5c9317743dc078f67693f"',
    'content-type': 'application/json'
}
# 获取某个字段
r.headers['Content-Type']
r.headers.get('content-type')
```

- cookie

```python
# 发送cookies
url = 'http://httpbin.org/cookies'
cookies = dict(cookies_are = 'working')
r = requests.get(url, cookies = cookies)
r.text

# cookie的返回对象是RequestCookieJar 可以传到Requests
jar = requests.cookies.RequestsCookieJar()
jar.set('tasty_cookie', 'yum', domain='httpbin.org', path='/cookies')
url = 'http://httpbin.org/cookies'
r = requests.get(url, cookies = jar)
print(r.text)
```

- 重定向

```python
# history追踪
import requests.get('http://github.com')
print(r.url)
print(r.history)

# allow_redirects = False 参数取消
r = requests.get('http://github.com', allow_redirects=False)
```

- 会话

```python
# 会话可以在跨请求过程中保持某些参数。同一个Session实力发出的所有请求之间可以保持cookie
import requests

s = requests.Session()
s.get('http://httpbin.org/cookies/set/sessioncookie/123456789')
r = s.get("http://httpbin.org/cookies")
print(r.text)

# 方法级别的参数不会被跨请求保持
s = requests.Session()
r = s.get('http://httpbin.org/cookies', cookies={'from-my': 'browser'})
# cookie存在
print(r.text)
# cookie空
r = s.get('http://httpbin.org/cookies')
print(r.text)

# 使用上下文管理器
with requests.Session() as s:
    s.get('http://httpbin.org/cookies/set/sessioncookie/123456789')
```

- 超时timeout

```python
r = requests.get('https://github.com', timeout=5)
```



### Xpath

### Re

### BeautifulSoup

### PyMysql

### Scrapy

- 目录解析
  - pro_name/
    - scrapy.cfg
    - pro_name/
      - items.py : 需要从网站提取的信息
      - pipelines.py : 储存数据管道
      - setting.py : 项目的配置文件
      - spider/ : 放置自己写的爬虫项目
        - xxx

- 常用指令

```shell
# 创建项目
scrapy startproject pro_name

# 创建一个新的爬虫
scrapy genspider [-t template] <name> <domain>
scrapy genspider -d basic

# 创建全站爬虫
scrapy genspider -t crawl xxx www.xxx.com

# 启动爬虫
scrapy crawl spider_name

# 检查爬虫 查看爬虫
scrapy check -l
scrapy list

# 传解析返回的回调函数
scrapy parse http://www.example.com/ -c parse_item

# 保存爬去数据到json中
scrapy crawl demo -o items.json
```



- Item

```python
import scrapy

# 进行类的属性绑定
# Field 对象指明了每个字段都是metadata
class demoItem(scrapy.Item):
    title = scrapy.Field()
    link = scrapy.Field()
```

```python
# 使用Product创建item
product = Product(name='PC', price=1000)
print(product)

# 获取字段的值
product['name']
product.get('price')

# 设置字段值
product['name'] = 'xxx'
```

```python
# 使用dict API
product.keys()
product.items()
```



- Spider

```python
# demo_spider.py 爬虫最基础样例
import scrapy

class demoSpider(scrapy.Spider):
    # 区分不同的spider
    name = 'demo'
    allowed_domains = ['www.xxx.com']
    # 启动爬虫时爬的页面
    start_urls = ['www.xxx.com']
    
    def parse(self, response):
        # 解析数据，提取出item
```

```python
# 一些selector
response.css()
response.xpath().extract()
response.re()
```

```python
# 在spider中使用item

import scrapy
from xx.items import demoItem
class demoSpider(scrapy.Spider):
    name = 'demo'
    allowed_domains = ['www.xxx.com']
    start_urls = ['www.xxx.com']
    
    def parse(self, response):
        for li in response.xpath('//ul/li'):
            item = demoItem()
            item['title'] = li.xpath('a/text()').extract()
            item['link'] = li.xpath('a/@href').extract()
            yield item
```

```python
# 返回多个Request和Item
import scrapy
from myproject.items import MyItem

class MySpider(scrapy.Spider):
    name = 'xxx.com'
    allowed_domains = ['xxx.com']
    start_urls = [
        'http://www.example.com/1.html',
        'http://www.example.com/2.html',
        'http://www.example.com/3.html',
    ]
    def parse(self,response):
        sel = scrapy.Selector(response)
        for h3 in response.xpath('//h3').extract():
            yield MyItem(title=h3)
        
        for url in response.xpath('//a/@href').extract():
            yield scrapy.Request(url, callback=self.parse)
```

```python
# 全站爬虫crawlspider
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule

class xxxSpider(CrawlSpider):
    name = 'xxx'
    start_urls = ['http://www.xxx.com']
    
    # 链接提取器：根据指定规则进行指定链接的提取
    link = LinkExtractor(allow=r'type=4&page=\d+')
    # 规则解析器：讲链接提取器进行指定规则的解析操作==>callback函数
    rules = (
    	Rule(line,callback='parse_item',follow=True),
    )
    
    def parse_item(self,response):
        print(response)
```

- setting

```python
# 一些常见的setting
```



### scrapy-redis

- 原生scrapy无法进行分布式爬虫，调度器和管道不能被分布式集群共享

- 基本流程

  - 创建一个工程文件
  - 创建一个CrawlSpider的爬虫文件 
  - 修改spider文件
    - 添加：from scrapy_redis.spiders import RedisCrawlSpider
    - 将start_urls和allowed_domains注释
    - 添加新属性：redis_key = 'proname' 
    - 写数据分析的操作
    - 把爬虫父类改成RedisCrawlSpider
  - 修改配置文件settings
    - 指定使用可以被共享的pipeline
    - 指定调度器
    - 指定redis服务器
  - redis配置
    - 修改redis.conf
      - 注释bind 127.0.0.1
      - 关闭 protected-mode no
    - 开启服务
      - redis-server 配置文件
    - 启动客户端
      - redis-cli
  - 执行工程
    - scrapy runspider xxx.py
  - 向调度器的队列中放入一个起始url
    - 调度器的队列在redis的客户端中
      - lpush name url
  -  数据存储到redis的proName:items

  ```python
  # 指定使用可以被共享的pipeline
  ITEM_PIPELINES = {
      'scrapy_redis.pipelines.RedisPipeline':400
  }
  
  # 指定调度器
  DUPEFILTER_CLASS = 'scrapy_redis.dupefilter.RFPDupeFilter'
  SCHEDULER = 'scrapy_redis.scheduler.Scheduler'
  SCHEDULER_PERSIST = True
  
  # 指定redis
  REDIS_HOST = '127.0.0.1' # redis远程服务器的ip
  REDIS_PORT = 8888
  ```

  

### 异步爬虫

### 多进程/线程/协程爬虫

### 增量式爬虫

- 监控网站，爬取最新的数据。
- 流程
  - 指定一个起始URL
  - 基于CrawlSpider获取其他页码链接
  - 基于Rule进行链接的请求
  - 从每一个页码对应的页面源码解析出URL请求然后解析和检测
  - 进行存储
  - 核心在于如何检测是否爬取存储过
    - 可以使用redis中的set结构
    - conn.sadd() 直到出现重复内容



### Mysql 

- 常用指令

```mysql
# 连接mysql服务器
mysql -u root -p
# 退出mysql
exit
# 创建数据库
CREATE DATABASE databaseName;
# 删除数据库
DROP DATABASE databaseName;
# 选择数据库
use databaseName;
```



- 在python中的常用操作

```python
import
```





### Selenium

- 安装方式

```shell
pip install Selenium
```

- 需要安装chromedrive
  - https://chromedriver.storage.googleapis.com/index.html?path=2.35/

- 八种方式进行元素定位

| 定位一个元素                      | 定位多个元素                       | 含义                  |
| :-------------------------------- | ---------------------------------- | --------------------- |
| find_element_by_id                | find_elements_by_id                | 通过id定位            |
| find_element_by_name              | find_elements_by_name              | 通过name定位          |
| find_element_by_xpath             | find_elements_by_xpath             | 通过xpath定位         |
| find_element_by_link_text         | find_elements_by_link_text         | 通过超链接定位        |
| find_element_by_partial_link_text | find_elements_by_partial_link_text | 通过部分链接定位      |
| find_element_by_tag_name          | find_elements_by_tag_name          | 通过标签定位          |
| find_element_by_class_name        | find_elements_by_class_name        | 通过类名进行定位      |
| find_elements_by_css_selector     | find_elements_by_css_selector      | 通过css选择器进行定位 |

- xpath的一些写法

```python
dr.find_element_by_xpath("//*[@id='xxx']")
dr.find_element_by_xpath("//*[@name='xxx']")
dr.find_element_by_xpath("//input[@class='xxx']")
dr.find_element_by_xpath("//input[@id='xxx' and @name='xxx']")
```

- css的一些写法

```python
dr.find_element_by_css_selector("#xxx")
dr.find_element_by_css_selector(".xxx")
dr.find_element_by_css_selector("html > body > div > span")
```

- 操控浏览器的一些方式

| 方法              | 说明                   |
| ----------------- | ---------------------- |
| set_window_side() | 设置浏览器的大小       |
| back()            | 控制浏览器后退         |
| forward()         | 控制浏览器向前         |
| refresh()         | 刷新当前页面           |
| clear()           | 清理文本               |
| send_keys()       | 模拟按键输入           |
| click()           | 单击元素               |
| sumbit()          | 提交表单               |
| get_attribute()   | 获取元素属性值         |
| is_displayed()    | 设置该元素是否用户可见 |
| size              | 返回元素尺寸           |
| text              | 获取元素文本           |

- 常用操作

```python
from selenium import webdriver
# 创建浏览器
driver = webdriver.Chrome(executable_path = "xxx\chromedrive")

# 通过浏览器发送URL请求
driver.get('www.baidu.com')

# 设置浏览器大小
driver.set_window_size(1400,800)

# 打印当前title URL
print(driver.title)
print(driver.current_url)

# 定位一组元素并遍历
elements = driver.find_elements_by_xpath('//div/a')

for e in elements:
    print(e.text)

# iframe切换
driver.switch_to.frame('xxx')
driver.switch_to.default_content()

# 文件上传
import os
file_path = 'file:///' + os.path.abspath('upfile.html')
driver.get(file_path)
driver.find_element_by_name("file").send_keys('D:\\upload_file.txt')

# 打印cookie
print(driver.get_cookies)

# 添加cookie
dict = {'name':'xxx','value':'xxx'}
driver.add_cookie(dict)

# 遍历cookie
for cookie in driver.get_cookies():
    print(cookie['name'],cookie['value'])

# 删除所有cookies
driver.delete_all_cookies()

# 关闭单个窗口
driver.close()

# 浏览器关闭
driver.quit()
```

- 键盘事件

| 模拟键盘按键                | 说明           |
| --------------------------- | -------------- |
| send_keys(Keys.BACK_SPACE)  | 删除键         |
| send_keys(Keys.SPACE)       | 空格键         |
| send_keys(Keys.TAB)         | 制表键         |
| send_keys(Keys.ESCAPE)      | 回退键         |
| send_keys(Keys.ENTER)       | 回车键         |
| send_keys(Keys.CONTROL,‘a’) | 全选（Ctrl+A） |
| send_keys(Keys.CONTROL,‘c’) | 复制（Ctrl+C） |
| send_keys(Keys.CONTROL,‘x’) | 剪切（Ctrl+X） |

