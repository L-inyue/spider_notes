| 爬虫 |        |
| - | :----- |
|作者|林月|
|内容|Scrapy框架|

## 创建项目
1. 创建项目
```python
scrapy startproject [爬虫名字]
```
2. 创建爬虫，进入到项目所在的路径，执行命令，**注意爬虫名称不能与项目名称一致**
```python
scrapy genspider [爬虫名字]	[爬取的域名]
```

## 项目结构目录

1.  items.py：用来存放爬虫爬取下来的数据模型
2.  middlewares.py：用来存放各种中间件的文件 
3.  pipelines.py：用来将items的模型存储到本地的磁盘中
4.  settings.py：本爬虫的一些配置信息（比如请求头，多久发一次请求，IP代理池等）
5.  scrapy.cdg：项目的配置文件
6.  spiders：以后所有的爬虫，都存放到这个里面


- **全局配置文件settings.py详解**

```python
# 1、定义User-Agent
USER_AGENT = 'Mozilla/5.0'
# 2、是否遵循robots协议，一般设置为False
ROBOTSTXT_OBEY = False
# 3、最大并发量，默认为16
CONCURRENT_REQUESTS = 32
# 4、下载延迟时间
DOWNLOAD_DELAY = 1
# 5、请求头，此处也可以添加User-Agent
DEFAULT_REQUEST_HEADERS={}
# 6、项目管道
ITEM_PIPELINES={
	'项目目录名.pipelines.类名':300
}
```
## **知识点汇总**

- **节点对象.xpath('')**

```python
1、列表,元素为选择器 ['<selector data='A'>]
2、列表.extract() ：序列化列表中所有选择器为Unicode字符串 ['A','B','C']
3、列表.extract_first() 或者 get() :获取列表中第1个序列化的元素(字符串)
```

- **日志变量及日志级别(settings.py)**     

```python
# 日志相关变量
LOG_LEVEL = ''
LOG_FILE = '文件名.log'

# 日志级别
5 CRITICAL ：严重错误
4 ERROR    ：普通错误
3 WARNING  ：警告
2 INFO     ：一般信息
1 DEBUG    ：调试信息
# 注意: 只显示当前级别的日志和比当前级别日志更严重的
```

- **管道文件使用**

```python
1、在爬虫文件中为items.py中类做实例化，用爬下来的数据给对象赋值
	from ..items import MaoyanItem
	item = MaoyanItem()
2、管道文件（pipelines.py）
3、开启管道（settings.py）
	ITEM_PIPELINES = { '项目目录名.pipelines.类名':优先级 }
```
## **数据持久化存储(MySQL)**

### **实现步骤**

```python
1、在setting.py中定义相关变量
2、pipelines.py中导入settings模块
	def open_spider(self,spider):
		# 爬虫开始执行1次,用于数据库连接
	def close_spider(self,spider):
		# 爬虫结束时执行1次,用于断开数据库连接
3、settings.py中添加此管道
	ITEM_PIPELINES = {'':200}

# 注意 ：process_item() 函数中一定要 return item ***
```

## **保存为csv、json文件**

- 命令格式

```python
scrapy crawl maoyan -o maoyan.csv
scrapy crawl maoyan -o maoyan.json
# settings.py中设置导出编码
FEED_EXPORT_ENCODING = 'utf-8'
```
## 总结
```python
# 流程:
1. 创建项目+爬虫文件
2.items.py:定义数据结构
3.spider.py:开始解析数据并提取
4.pipelines:处理数据
5.setting.py:全局配置
6.run.py:运行爬虫
# response的方法:
1. response.xpath("")
2. response.text:字符串
3. response.body:字节串
# 选择器对象列表
xxx.xpath("").extract()
xxx.xpath("").extract_first()
xxx.xpath("").get()
xxx.xpath("").getall()
# 重写start_request()方法
1.去掉start_urls变量
2.def start_request():
# settings.py中常用的变量:
1.LOG_LEVEL=""
2.LOG_FILE=""
3.FEED_EXPORT_ENCODING=''

# 保存为csv或者json文件
scrapy crawl spider  -o  xxx.csv
scrapy crawl spider  -o  xxx.json

# 存数据库
class xxxPipeline(object):
		def	open_spider(self,spider):
				pass
		def process_item(self,item,spider)
				pass
		def close_spider(self,spider):
				pass

```
## **settings.py常用变量**

```python
# 1、设置日志级别
LOG_LEVEL=''
# 2、保存到日志文件(不在终端输出)
LGO_FILE='xxx.log'
# 3、设置数据导出编码(主要针对于json文件)
FEED_EXPORT_ENCODING='utf-8'
# 4、非结构化数据存储路径
linux
IMAGES_STORE="/home/linyue/so/images"
window:
  IMAGES_STORE="D:\\so\\images"  
  IMAGES_STORE="D:/so/images"  
# 5、设置User-Agent
User-Agent-"Mozilla/5.0   xxx"
# 6、设置最大并发数(默认为16)
CONCURRENT_REQUESTS=32
# 7、下载延迟时间(每隔多长时间请求一个网页)
DOWNLOAD_DELAY=0.5
# 8、请求头
DEFAULT_REQUEST_HEADERS={
    'cookies':'',
    'referer':''.
    'User-Agent':'',
}
# 9、添加项目管道
ITEM_PIPELINES={
    '项目目录名.Pipelines.类名':优先级1-1000
}
# 10、添加下载器中间件
DOWNLOADER_MIDDLEWARES={
       '项目目录名.middlewares.类名':优先级1-1000
}
# 11.cookies:默认禁用cookies
COOKIE_ENADLED=False	|		True
设置为True或者Flase都是开启cookies
	# 1.False:找settings.py中DEFAULT_REQUEST_HEADERS中的cookies
	# 2. True:在爬虫文件scrapy.FormRequest()中的cookies参数中查找
```
**scrapy添加cookie的三种方式**

```python
# 1、修改 settings.py 文件
1、COOKIES_ENABLED = False  取消注释
2、DEFAULT_REQUEST_HEADERS = {}   添加Cookie

# 2、爬虫文件 - 利用cookies参数
COOKIES_ENABLED = TRUE  # 找 Request()方法中cookies参数
def start_requests(self):
    yield scrapy.Request(url=url,cookies={},callback=xxx)
    
# 3、DownloadMiddleware设置中间件
COOKIES_ENABLED = TRUE # 找Request()方法中cookies参数
def process_request(self,request,spider):
    request.cookies={}
```

## 分布式爬虫
* 原理
1. 多台主机共享一个爬取的队列

* 实现
1. 重写scrapy的调度器(scrapy_redis)

## **设置中间件**

**随机User-Agent**

```python
# 1、middlewares.py
class RandomUaDownloaderMiddleware(object):
    def process_request(self,request,spider):
    	request.header['User-Agent'] = xxx
# 2、settings.py
DOWNLOADER_MIDDLEWARES = {'xxx.middlewares.xxx':300}
```

**随机代理**

```python
# 1、middlewares.py
class RandomProxyDownloaderMiddleware(object):
    def process_request(self,request,spider):
    	request.meta['proxy'] = xxx
        
    def process_exception(self,request,exception,spider):
        return request
# 2、settings.py
DOWNLOADER_MIDDLEWARES = {'xxx.middlewares.xxx':200}
```

## **分布式爬虫**

### **分布式爬虫介绍**

- **原理**

```python
多台主机共享1个爬取队列
```

- **实现** 

```python
重写scrapy调度器(scrapy_redis模块)
```

- **为什么使用redis**

```python
1、Redis基于内存,速度快
2、Redis非关系型数据库,Redis中集合,存储每个request的指纹
3、scrapy_redis安装
	sudo pip3 install scrapy_redis
```

## **scrapy_redis详解**

- **GitHub地址**

  ```python
  https://github.com/rmax/scrapy-redis
  ```

- **settings.py说明**

  ```python
  # 重新指定调度器: 启用Redis调度存储请求队列
  SCHEDULER = "scrapy_redis.scheduler.Scheduler"
  
  # 重新指定去重机制: 确保所有的爬虫通过Redis去重
  DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"
  
  # 不清除Redis队列: 暂停/恢复/断点续爬/增量爬取
  SCHEDULER_PERSIST = True
  
  # 优先级队列 （默认）
  SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.PriorityQueue'
  #可选用的其它队列
  # 先进先出队列
  SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.FifoQueue'
  # 后进先出队列
  SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.LifoQueue'
  
  # redis管道
  ITEM_PIPELINES = {
  	# 真正把数据存入redis数据库的
      'scrapy_redis.pipelines.RedisPipeline': 300
  }
  
  #指定连接到redis时使用的端口和地址
  REDIS_HOST = 'localhost'
  REDIS_PORT = 6379
  ```



## Scrapy爬虫笔记
1. response是一个 “ scrapy.http.response.html.HtmlResponse ” 对象，可以执行“xpath”和”css“提取数据

   

2. 提取出来的数据是一个“Selector”或者是一个 “ SelectorList ” 对象，如果想要获取其中的字符串，那么应该执行“getall”或者“get”方法			（.root）

   

3. getall方法：获取”Selector“中的所有文本，返回一个列表

   

4. get方法：获取“Selector”中的第一个文本，返回一个str类型

   

5. 如果数据解析回来，要么传给pipelines，那么可以使用“yield”来返回，或者是搜集所有item，最后统一返回

   

6. item：建议在“items.py”中定义好模型，以后就不要使用字典 

   

7. pipelines：这个是专门用来保存数据的，其中是三个方法是经常用的。
   - “open_spider(self,spider)”：当爬虫被打开的时候执行
   - “process_item(self,item,spider)”：当爬虫有item传过来的时候会被调用
   - “close_spider(self,spider)”：当爬虫关闭时调用。

   要激活pipelines，应该在“settings.py”中，设置“ITEM_PIPELINES”。实例如下：

   ```python
   '爬虫项目名称.pipelines.爬虫名称Pipeline':300,
   ```

   

## JsonItemExporter和JsonLinesItemsExporter：

保存数据 json数据的时候，可以使用这个两个类，让操作变得更简单
1. “JsonItemExporter”：这个是每次把数据添加到内存中，最后一次统一写入到磁盘中。
	好处：存储的数据是一个满足json规则的数据
	坏处：如果数据较大，会比较消耗内存
	

	示例代码：
	
	```python
	
	```

2. “JsonLine：sItemsExporter”：这个是每次调用“export_item”的时候就把这个item存储到硬盘中。
	好处：每次处理数据的时候就直接存储到硬盘中，这样不会消耗内存，数据也比较安全
	坏处：每一个字典是一行，这个文件不是一个满足json格式的文件

	示例代码：
   ```python
	self.exporter = JsonLinesItemExporter(self.fp, ensure_ascii=False, encoding='utf-8')
	```
	
	## CrawlSpider
	需要使用“LinkExtractor”和“Rule”.这两个东西决定爬虫的具体走向
	1. allow设计规则的方法：
		* 要能够限制在我们想要的url上面，不要跟其他的url产生相同的正则表达式
	2. 什么情况下使用follow:
		* 如果在爬取页面的时候，需要将满足当前条件的url在进行跟进，那么设置为TRUE，否者设置为Fasle
	3. 什么时候使用callback【回调函数】:
		* 如果这个url对应的页面，只是为了获取更多的url，可以不需要callback，如果需要获取ur对应页面中的数据，那么就需要指定一个callback

##  scrapy shell
1. 可以方便我们做一些数据提取的测试代码
2. 如果想要执行scrapy命令，那么需要先进入到scrapy所在的目录
3. 如果想要读取某个项目的配置信息，那么应该先进入到项目中，再执行这个‘scrapy shell’的命令

##  模拟登录人人网
1. 想要放松post请求，那么推荐使用‘scrapy FormRequest’的方法，可以方便的指定表单数据
2. 如果想一开始发送post请求，那么应该重写‘start_requests’方法。在这个方法中，发送post请求

```python
    def start_requests(self):
        # 重写方法，改为post请求
        # 登录接口

        url = 'http://www.renren.com/PLogin.do'
        data = {'email': '2117440168@qq.com', 'password': "baby962464"}
        request = scrapy.FormRequest(url, formdata=data, callback=self.parse_page)
        yield request

    def parse_page(self, response):
        print("进来")
        request = scrapy.Request(url="http://www.renren.com/880151247/profile/", callback=self.parse_profile)
        yield request

    def parse_profile(self,response):
        with open('dp.html','w',encoding='utf-8') as fp:
            fp.write(response.text)
```

## **爬虫总结**

```python
# 1、什么是爬虫
  爬虫是请求网站并提取数据的自动化程序

# 2、robots协议是什么
  爬虫协议或机器人协议,网站通过robots协议告诉搜索引擎哪些页面可以抓取，哪些页面不能抓取

# 3、爬虫的基本流程
  1、请求得到响应
  2、解析
  3、保存数据

# 4、请求
  1、urllib
  2、requests
  3、scrapy

# 5、解析
  1、re正则表达式
  2、lxml+xpath解析
  3、json解析模块

# 6、selenium+browser

# 7、常见反爬策略
  1、Headers : 最基本的反爬手段，一般被关注的变量是UserAgent和Referer，可以考虑使用浏览器中
  2、UA ： 建立User-Agent池,每次访问页面随机切换
  3、拉黑高频访问IP
     数据量大用代理IP池伪装成多个访问者,也可控制爬取速度
  4、Cookies
     建立有效的cookie池，每次访问随机切换
  5、验证码
    验证码数量较少可人工填写
    图形验证码可使用tesseract识别
    其他情况只能在线打码、人工打码和训练机器学习模型
  6、动态生成
    一般由js动态生成的数据都是向特定的地址发get请求得到的，返回的一般是json
  7、签名及js加密
    一般为本地JS加密,查找本地JS文件,分析,或者使用execjs模块执行JS
  8、js调整页面结构
  9、js在响应中指向新的地址

# 8、scrapy框架的运行机制

# 9、分布式爬虫的原理
  多台主机共享一个爬取队列
```

