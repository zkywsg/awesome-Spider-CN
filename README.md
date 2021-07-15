# awesome-Spider-CN
### 微博

### Bilibili

<br/>

### Requests



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

