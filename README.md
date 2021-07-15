# awesome-Spider-CN
### 微博

### Bilibili

### 百度指数

### Twitter

### 秀动



<br/>

### Requests

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

### 异步爬虫
