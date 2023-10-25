# 第一章 爬虫基础

## URL格式规范：

>  scheme://[username:password@]hostname[:port][/path][:parameters][?query][#fragment]

 

-  scheme：协议。常用的协议有http、https等，也称protocol
-  username、password：用户名和密码
-  hostname：主机地址
-  port：端口
-  path：路径
-  parameters：参数。用来指定访问某个资源时的附加信息。但是parameters现在用得很少。所以目前很多人会把该参数后面的query部分称作参数
-  query：查询。用来查询某类资源。若有多个查询，则用&隔开
-  fragment：片段。它是对资源描述的部分补充

 

## HTTP 请求方法

| 方法    | 描述                                                        |
| ------- | ----------------------------------------------------------- |
| GET     | 请求页面，并返回页面内容                                    |
| HEAD    | 类似于GET请求，只不过返回的响应仲没有具体内容。用于获取报头 |
| POST    | 大多数用于提交表单或上传文件、数据包含在请求体中            |
| PUT     | 客户端传向服务器的数据取代指定文档中的内容                  |
| DELETE  | 请求服务器删除指定的页面                                    |
| CONNECT | 把服务器当作跳板，让服务器代替客户端访问其他页面            |
| TRACE   | 回显服务器收到请求。主要用于测试或诊断                      |

 

## Content-Type 和POST 提交数据方式的关系

| Content-Type                      | POST提交数据的方式 |
| --------------------------------- | ------------------ |
| application/x-www-form-urlencoded | 表单数据           |
| multipart/form-data               | 表单文件上传       |
| application/json                  | 序列化JSON数据     |
| text/xml                          | XML 数据           |

## 常见的错误状态码及错误原因

| 状态码 | 说明           | 详情                                                         |
| ------ | -------------- | ------------------------------------------------------------ |
| 100    | 继续           | 请求者应当继续提出请求。服务器已接受到请求的一部分，正则等待其余部分 |
| 101    | 切换协议       | 请求者已要求服务器切换协议，服务器已确认并准备切换           |
| 200    | 成功           | 服务器已成功处理了请求                                       |
| 201    | 已创建         | 请求成功并且服务器创建了新的资源                             |
| 202    | 已接收         | 服务器已接收请求，但尚未处理                                 |
| 203    | 非授权信息     | 服务器已成功处理了请求，但返回的信息可能来自另一个源         |
| 204    | 无内容         | 服务器成功处理了请求，但没有返回任何内容                     |
| 205    | 重置内容       | 服务器成功处理了请求，内容被重置                             |
| 206    | 部分内容       | 服务器成功处理了部分请求                                     |
| 300    | 多种选择       | 针对请求，服务器可执行多种操作                               |
| 301    | 永久移动       | 请求的网页已永久移动到新的位置，即永久重定向                 |
| 302    | 临时移动       | 请求的网页暂时跳转到其他页面，即暂时重定向                   |
| 303    | 查看其他位置   | 如果原来的请求是POST，重定向目标文档应该通过GET提取          |
| 304    | 未修改         | 此次请求返回的网页未经修改，继续使用上次的资源               |
| 305    | 使用代理       | 请求者应该使用代理访问该网页                                 |
| 307    | 临时重定向     | 临时从其他位置响应请求的资源                                 |
| 400    | 错误请求       | 服务器无法解析该请求                                         |
| 401    | 未授权         | 请求没有进行身份验证或验证未通过                             |
| 403    | 禁止访问       | 服务器拒绝此请求                                             |
| 404    | 未找到         | 服务器找不到请求的网页                                       |
| 405    | 方法禁用       | 服务器禁用了请求中指定的方法                                 |
| 406    | 不接收         | 无法使用请求的内容响应请求的网页                             |
| 407    | 需要代理授权   | 请求者需要使用代理授权                                       |
| 408    | 请求超时       | 服务器请求超时                                               |
| 409    | 冲突           | 服务器在完成请求时发生冲突                                   |
| 410    | 已删除         | 请求的资源已永久删除                                         |
| 411    | 需要有效程度   | 许文强不接收不含有效内容长度标头字段的请求                   |
| 412    | 未满足前提条件 | 服务器未满足请求者在请求中设置的某一个前提条件               |
| 413    | 请求实体过大   | 请求实体过大，超出服务器的处理能力                           |
| 414    | 请求URL过长    | 请求网址过长，服务器无法处理                                 |
| 415    | 不支持类型     | 请求格式不被请求页面支持                                     |
| 416    | 请求范围不符   | 羊肉面无法提供请求的范围                                     |
| 417    | 未满足期望值   | 服务器未满足期望请求标头字段的要求                           |
| 500    | 服务器内部错误 | 服务器遇到错误，无法完成请求                                 |
| 501    | 未实现         | 服务器不具备完成请求的能力                                   |
| 502    | 错误网关       | 服务器作为网关或代理，接收到上游服务器的无效响应             |
| 503    | 服务不可用     | 服务器目前无法使用                                           |
| 504    | 网关超时       | 服务器作为网关或代理，没有及时从上游服务器接收到请求         |
| 505    | HTTP版本不支持 | 服务器不支持请求中使用的HTTP协议版本                         |

 

 

## HTTP 1.X 与 HTTP2.0 区别：

1. 二进制分层帧

2. 1. HTTP 1.x 使用文本方式传输
   2. HTTP 2.0 使用二进制方式传输

3. 多路复用

4. 1. HTTP 1.X：n个并行请求需要n个TCP链接，而且浏览器为了控制资源，还会对单个域名有6-8各TCP连接请求的限制
   2. HTTP 2.0：同域名只需要占用一个连接

5. 流控制

6. 1. HTTP 2.0 自带流控制方法，虽然TCP也拥有流控制方法，但 HTTP2.0 的更高效更精准

7. 服务端推送

8. 1. 服务器可针对一个i请求发生多个响应，但为了保证安全，服务器需要遵守同源策略

9. 

10. #### 节点树DOM

11. ![image-20230509165303488](C:/Users/19374/AppData/Roaming/Typora/typora-user-images/image-20230509165303488.png)

## 节点数及书中节点间的关系

![image-20230509165319949](C:/Users/19374/AppData/Roaming/Typora/typora-user-images/image-20230509165319949.png)

## Session 与 Cookie

**无状态HTTP**：HTTP的无状态是指HTTP协议对事物处理是没有记忆能力的，若遇到需要登录的网站，则会出现：一个请求一次登录的情况

 

**Session**：存储在Session对象中的变量将不会丢失。当Session过期或被抛弃后，服务器将终止该Session

 

**Cookie**：Cookie，指某些网站为了鉴别用户身份、进行Session跟踪而存储在用户本地终端上的数据（用户身份凭证）

 

**Session维持**：服务器会在第一个响应头中返回带有 Set Cookie 字段的报头，客户端会把Cookie保存起来，以便下一次请求网站时使用，同时Cookie中携带了Session ID的相关信息，服务器通过Cookie即可找到响应的Session，继而通过Session判断用户状态，若Session有效，即证明用户处于登录状态，反之，若传给服务器的Cookie无效，或者Session已经过期了，则需要重新登录

 

Cookie和Session需要配合，一个在客户端，一个在服务器，二者共同协作，实现了登录控制



#### 属性结构

![image-20230509165412680](C:/Users/19374/AppData/Roaming/Typora/typora-user-images/image-20230509165412680.png)



Name：Cookie名称。一旦创建，便不可更改

Value：Cookie的值

Domain：指定可以访问该Cookie的域名

Path：Cookie的使用路径

Max-Age：Cookie失效的时间，单位为秒，常和Expires配合使用

Size：Cook的大小

HttpOnly：Cookie的HttpOnly属性，若此属性为True，则只有在Http Headers中才会带有此Cookie信息，而不能通过document.cookie 来访问此Cookie（可用于防御 XSS）

Secure：是否允许使用安全协议传输Cookie





**_常见误区_**

在谈论关于Session机制的时候，常会听到一种误解——只要关闭浏览器，Session就会消失，但其实都是在我们注销操作时才会删除Session

误解原因：因为Session ID保存在浏览器中，而我们关闭浏览器的时候，Cookie就会失效。但我们可以使用将Cookie保存到硬盘上或修改浏览器发出的HTTP请求，把原来的Cookie发给服务器，那么再次打开浏览器时，任能找到原来的Session ID

 

 ## 代理的基本原理

1. 基本原理：网络信息的中转站。设置代理服务器，就算再客户端和服务器之间搭建一座桥梁，客户端会把请求发送给代理服务器，由代理服务器转发请求，当然，回复也将由代理服务器转发给客户端

2. 代理的作用：

3. 1. 突破自身IP的访问限制
   2. 访问一些单位或团体的内部资源（通过IP判断是否有访问权限）
   3. 提高访问速度（代理服务器会设置一个较大的硬盘缓冲区，以便用户访问相同信息时，直接从缓冲区获取）
   4. 隐藏真实IP

 

**_根据匿名程度区分_**

-  高度匿名访问：高度匿名代理会将数据包原封不动地转发，服务端看来似乎真的使用一个普通客户端在访问，记录的IP则是代理服务器的IP
-  普通匿名访问：普通匿名代理会对数据包做一些改动，此时会有一定的改了去追查客户端的真实IP。这里代理服务器通常会加入HTTP头；HTTP_VIA     和 HTTP_X_FORWARDED_FOR
-  透明代理：这种代理不会改动数据包且会告诉服务端客户端的真实IP。代理功能：缓存技术提高浏览速度，内容过滤提高安全性；常见于防火墙
-  间谍代理：由组织或个人创建的代理服务器，用于记录用户传输的数据，然后对数据进行研究、监控

## 多线程和多进程的基本原理

 

**_多进程和多线程的区别：_**

打开一个程序即为打开一个进程，在程序中运行N个任务即位在运行N个进程

 

**_并发和并行_**

并发：多个线程对应的多条执行被快速轮询

并行：同一时刻有多条指令在多个处理器上同时执行（只能存在于多处理器系统中）

IOS密集型：CPU在执行一段程序时，可能会出现等待某个资源完成的情况

CPU密集型：CPU不会出现等待的情况（此时在多线程中，轮询就只会徒增开销）

 

**_Python 中的多线程和多进程_**

由于一个Python进程中，GIL只有一个。这样会导致：一个Python进程中的多个线程在同一时刻以至能执行一个；即Python多进程香米多线程，允许效率在多核情况下比单核会有成倍的提升

 

 

## Urilib的使用

urllib包含如下4模块：

-  request：模拟请求的发送
-  error：异常处理模块
-  parse：工具模块。提供了许多URL的处理方法
-  rebotparser：主要用来识别网站的robots.txt文件，判断哪些网站可以爬，哪些网站不可以爬



# 第二章 基本库的使用

## 2.1 urllib的使用

-   request：基本的HTTP请求模块，可以模拟请求的发送。只需要给库方法传入URL以及额外的参数即可
-   erroe：异常处理模块。捕获异常
-   parse：一个工具模块。提供了许多URL的处理方法，例如：拆分、解析、合并等
-   rebotparser：用来识别网站的 robot 网站，判断哪些网站可爬哪些网站不可爬

 

### data参数：

在添加该参数时，必须使用bytes方法将参数转换为字节流编码格式的内容，即bytes类型

```python
data=bytes(parse.urlencode(dict1),encoding='utf-8')
```

 

### timeout参数：

用于设置超时时间

```python
urllib.request.urlopen(target,timeout=3.5).read().decode('utf-8','ignore')
```

### Request

```
利用urlipen方法可以发起最基本的请求，但无法在请求中加入需要的handler等信息。此时就需要使用Request
 
Request构造方法如下：
class.urllib.request.Request(url,data=None,headers={},origin_req_host=None,unverifiable=False,method=None)
第一个参数：url用于请求URL，这是必传参数，其他参数都是可选
第二个参数：data用于传输数据，必须时bytes类型。如果数据是字典，可以先用urllib.parse模块里的urlencode方法进行编码
第三个参数：headers是一个字典，这是请求头
第四个参数：origin_req_host 请求方的host名称和IP地址
第五个参数：unverifiable 表示是请求是否无法验证的，默认取值是False，意思是用户没有足够的权限来接收这个请求的结果
第六个参数：method 用于指示请求方法；POST、GET
```



```python
from urllib import request,parse
 
url='https://www.httpbin.org/post'
 
headers={
'User-Agent':'Mozilla/4.0(compatible;MSIE7.0;WindowsNT6.0;AcooBrowser;SLCC1;.NETCLR2.0.50727;MediaCenterPC5.0;.NETCLR3.0.04506)',
'host':'www.httpbin.org'
}
 
dict1={'name':'germey'}
#添加data参数时必须使用bytes方法将参数转化为字节流编码格式的内容
data=bytes(parse.urlencode(dict1),encoding='utf-8')
req=request.Request(url,data=data,headers=headers,method='POST')
response=request.urlopen(req)
print(response.read().decode('utf-8'))
```

**_运行结构_**

```python
		{
		  "args": {}, 
		  "data": "", 
		  "files": {}, 
		  "form": {
		    "name": "germey"
		  }, 
		  "headers": {
		    "Accept-Encoding": "identity", 
		    "Content-Length": "11", 
		    "Content-Type": "application/x-www-form-urlencoded", 
		    "Host": "www.httpbin.org", 
		    "User-Agent": "Mozilla/4.0(compatible;MSIE7.0;WindowsNT6.0;AcooBrowser;SLCC1;.NETCLR2.0.50727;MediaCenterPC5.0;.NETCLR3.0.04506)", 
		    "X-Amzn-Trace-Id": "Root=1-63dd1d22-4b26a3a57e78727d7b522009"
		  }, 
		  "json": null, 
		  "origin": "133.18.199.123", 
		  "url": "https://www.httpbin.org/post"
		}

```

##### 高级用法：

```
HTTPDefaultErrorHandler：用于处理HTTP响应错误，所有错误都会抛出HTTPError类型的异常
HTTPRedirectHandler：用于处理重定向
HTTPCookieProcesser：用于处理Cookie
ProxyHandler：用于设置代理，代理默认为空
HTTPPasswordMgr：用于管理密码，维护着用户名密码对照表
HTTPBasicAuthHandler：用于管理认证，如果一个链接在打开时需要认证，那么可以用这个类来解决问题
```

##### 基本身份验证通过：

![image-20230509170745327](1.%20Python%20%E7%BD%91%E7%BB%9C%E7%BC%96%E7%A8%8B%E5%BC%80%E5%8F%91%E5%AE%9E%E6%88%98%20-%20%E7%AC%AC%E4%B8%80%E7%AB%A0.assets/image-20230509170745327.png)



```python
from urllib.request import HTTPPasswordMgrWithDefaultRealm,HTTPBasicAuthHandler,build_opener
from urllib.errori mport URLError
		
username='admin'
password='admin'
url='https://ssr3.scrape.center/'
		
#实例化这个类
p = HTTPPasswordMgrWithDefaultRealm()
#利用add_password方法添加用户名和密码
p.add_password(None,url,username,password)
#以上两步建立了一个用来处理认证的handler类
		
auth_handler=HTTPBasic AuthHandler(p)
#将auth_handler类当作参数传入build_opener方法，构建Opener
opener=build_opener(auth_handler)
		
try:
	result=opener.open(url)
	html=result.read().decode('utf-8')
	print(html)
except URL Error as e:
	print(e.reason)

```

##### HTTP代理设置：

```python
from urllib.error import URLError
from urllib.request import ProxyHandler, build_opener

# 使用本地代理--clash默认端口：7890
proxy_handler = ProxyHandler({
    'http': 'http://127.0.0.1:7890',
    'https': 'http://127.0.0.1:7890'
})

# build_opener构建opener
opener = build_opener(proxy_handler)

try:
    response = opener.open('http://www.baidu.com')
    print(response.read().decode('utf-8'))
except URLError as e:
    print(e.reason)

```

##### 查看 Request Cookie

```python
import http.cookiejar
import urllib.request

# 声明Cookiejar对象
cookie = http.cookiejar.CookieJar()
# 利用HTTPCookieProcessor构造handler
handler = urllib.request.HTTPCookieProcessor(cookie)
# build_opener构建opener
opener = urllib.request.build_opener(handler)
response = opener.open('http://www.baidu.com')
for item in cookie:
    # print(item)
    print(item.name + '=' + item.value)

```



==运行结果：==

```python
BIDUPSID=359FD9DAF56B8E2F8EB29F53D603BB2E
H_PS_PSSID=36559_38093_37911_37990_37801_37920_38089_26350_37959_38098_38008_37881
PSTM=1675435894
BDSVRTM=0
BD_HOME=1
```

##### 保存网站Cookie

```python
import urllib.request
import http.cookiejar

"""
需要将cookiejar换成MozillaCookieJar，它会在生成文件时用到，时cookiejar的子类，
可以用来处理跟Cookie和文件相关的事件，例如：保存和读取Cookie

也可保存为LWP格式的cookie文件
"""

filename = 'cookie.txt'
cookie = http.cookiejar.LWPCookieJar(filename)
handler = urllib.request.HTTPCookieProcessor(cookie)
opener = urllib.request.build_opener(handler)
response = opener.open('http://www.baidu.com')
cookie.save(ignore_discard=True, ignore_expires=True)

```

==保存结果==

```python
#LWP-Cookies-2.0
SetCookie3:BAIDUID="88B372CFB2F038B48297B6E35C4F734B:FG=1";path="/";domain=".baidu.com";path_spec;domain_dot;expires="2024-02-0314:51:12Z";comment=bd;version=0
SetCookie3:BIDUPSID=88B372CFB2F038B40482C81B9910FB05;path="/";domain=".baidu.com";path_spec;domain_dot;expires="2091-02-2118:05:19Z";version=0
SetCookie3:H_PS_PSSID=36553_38092_38059_37911_37989_36805_37923_38089_26350_37958_38096_38008_37881;path="/";domain=".baidu.com";path_spec;domain_dot;discard;version=0
Set-Cookie3:PSTM=1675435873;path="/";domain=".baidu.com";path_spec;domain_dot;expires="2091-02-2118:05:19Z";version=0
Set-Cookie3:BDSVRTM=23;path="/";domain="www.baidu.com";path_spec;discard;version=0
Set-Cookie3:BD_HOME=1;path="/";domain="www.baidu.com";path_spec;discard;version=0

```

##### 读取并利用Cookie文件

```python
import urllib.request
import http.cookiejar

"""
这样做的前提是生成了LWPCookieJar格式的Cookie并保存
"""

cookie = http.cookiejar.LWPCookieJar()
# 调用load方法读取本地的Cookie文件，获取Cookie内容
cookie.load('cookie.txt', ignore_expires=True, ignore_discard=True)
handler = urllib.request.HTTPCookieProcessor(cookie)
opener = urllib.request.build_opener(handler)
response = opener.open('http://www.baidu.com')
print(response.read().decode('utf-8'))

```

### 异常处理

**request模块产生的异常都可以通过捕获这个类来处理，它具有一个reason属性，即返回错误原因**

  ```python
from urllib import request, error

try:
    response = request.urlopen('https://cuiaingcal.com/404')
except error.URLError as e:
    print(e.reason)

  ```

### HTTPError

**HTTPError是URLError的子类，专门用来处理HTTP请求错误，具有以下3个属性：**

*  code：返回HTTP状态码

*  reason：同父类一样，返回错误原因

*  headers：返回请求头



```python
from urllib import request, error

try:
    response = request.urlopen('http://www.baidu.com/404')
except error.HTTPError as e:
    print(e.reason, e.code, e.headers, sep='\n')

```

==运行结果==

```python
Not Found
404
Content-Length: 201
Content-Type: text/html; charset=iso-8859-1
Date: Mon, 06 Feb 2023 15:11:32 GMT
Server: Apache
Connection: close

```

由于URLError是HTTPError的父类，所以可以**先捕获子类的错误，再捕获父类的错误**

```python
from urllib import request, error

# 由于URLError是HTTPError的父类，所以可以先捕获子类的错误、再捕获父类的错误
try:
    response = request.urlopen('https://cuiqingcal.com/404')
except error.HTTPError as e:
    print(e.reason, e.code, e.headers)
except error.URLError as e:
    print(e.reason)
else:
    print('RequestSuccessfully')
```

### urlparse

该方法可以实现URL的识别和分段，可用**属性名和索引值来获取响应的内容**

```python
from urllib.parse import urlparse

result = urlparse('www.baidu.com/index.html;user?id=5#comment')
print(type(result))
print(result)
```

==结果==

```python
<class 'urllib.parse.ParseResult'>
ParseResult(scheme='https', netloc='', path='www.baidu.com/index.html', params='user', query='id=5', fragment='comment')
字段分别为：协议       域名    路径                         参数         查询   定位页面内部的下拉位置
```

### urlunparse

与urlparse相反，用于构造URL。这个方法是一个可迭代对象，其长度必须是6，否则会报错

```python
from urllib.parse import urlunparse
		
data=['https','www.baidu.com','index.html','user','a=6','comment']
print(urlunparse(data))
```

==结果==

```
https://www.baidu.com/index.html;user?a=6#comment
```

### urljoin：通过一个链接生成一个新链接

base_url提供三个内容：scheme、netloc和path，如果新的链接不存在则给予补充；如果存在，就使用新的，则base_url不起作用

```python
urljoin(base_url,新链接)
```

```python
from urllib.parse import urljoin

print(urljoin('http://www.baidu.com', 'FAQ.html'))
print(urljoin('http://www.baidu.com', 'http://www.cuiqingcai.com/FAQ.html'))
print(urljoin('http://www.baidu.com/about.html', 'http://www.cuiqingcai.com/FAQ.html'))
print(urljoin('http://www.baidu.com/about.html', 'http://www.cuiqingcai.com/FAQ.html?question=2'))
print(urljoin('http://www.baidu.com/about.html', 'http://www.cuiqingcai.com/index.html'))
print(urljoin('http://www.baidu.com/about.html', '?category=2#comment'))
```

==结果==

```URL
http://www.baidu.com/FAQ.html
http://www.cuiqingcai.com/FAQ.html
http://www.cuiqingcai.com/FAQ.html
http://www.cuiqingcai.com/FAQ.html?question=2
http://www.cuiqingcai.com/index.html
http://www.baidu.com/about.html?category=2#comment
```

### urlencode

将查询参数做序列化，构建新的GET请求

```python
from urllib.parse import urlencode

params = {
    'name': 'germey',
    'age': 20
}

base_url = 'http://www.baidu.com?'
url = base_url + urlencode(params)
print(url)

```

==结果==

```
http://www.baidu.com?name=germey&age=20
```

### parse_qs

有了序列化，必然会有反序列化，即将GET请求转回字典

```python
from urllib.parse import parse_qs
		
query='name=germey&age=20'
print(parse_qs(query))
```

==结果==

```python
{'name': ['germey'], 'age': ['20']}
```

### parse_qsl

与parse_qs区别是：

+  parse_qs将参数转为字典

*  parse_qsl将参数转为由元组组成的列表

```python
from urllib.parse import parse_qsl
		
query='name=germey&age=20'
print(parse_qsl(query))
```

==结果==

```python
[('name', 'germey'), ('age', '20')]
```

### quote

该方法可以将内容转化为URL编码的格式（可用于搜索语句的构建等，对中文搜索有很大的帮助）

```python
from urllib.parse import quote
from urllib.parse import unquote

keyword = '壁纸'
url = 'http://www.baidu.com?wd=' + quote(keyword)
print(url)
print(unquote(url))
```

==结果==

```
http://www.baidu.com?wd=%E5%A3%81%E7%BA%B8
```

### unquote

将URL编码结果，利用unquote还原

```python
from urllib.parse import quote
from urllib.parse import unquote
		
keyword='壁纸'
url='http://www.baidu.com?wd='+quote(keyword)
print(url)
print(unquote(url))
```

==结果==

```
http://www.baidu.com?wd=壁纸
```

## 2.2 Request 的基本使用

### 基本实例--最简单的GET请求

```python
 import requests
	
data={
	'name':'germy',
	'age':25
}
#requests模块可直接使用params参数构造URL，以代替urllib.parse.urlencode功能
r=requests.get('https://www.httpbin.org/get',params=data)
print(r.text)
```

```python
 {
	"args": {
		"age": "25", 
	    "name": "germy"
	  }, 
	"headers": {
		"Accept": "*/*", 
	    "Accept-Encoding": "gzip, deflate", 
	    "Host": "www.httpbin.org", 
	    "User-Agent": "python-requests/2.20.0", 
	    "X-Amzn-Trace-Id": "Root=1-63f4d43e-13ae9f1c05872c3c350167e7"
	  }, 
	 "origin": "113.118.81.169", 
	 "url": "https://www.httpbin.org/get?name=germy&age=25"
}

```

### 构造URL query选项

```python
import requests
	
data={
	 'name':'germy',
	 'age':25
	}
	
r=requests.get('https://www.httpbin.org/post',params=data)
print(r.text)
```

  ==结果==

```python
 {
	"args": {
	"age": "25", 
	"name": "germy"
}, 
	"headers": {
	"Accept": "*/*", 
	"Accept-Encoding": "gzip, deflate", 
	"Host": "www.httpbin.org", 
	"User-Agent": "python-requests/2.20.0", 
	"X-Amzn-Trace-Id": "Root=1-63f4d52e-3ba4df4015bb446e01e76def"
}, 
	"origin": "113.118.81.169", 
	"url": "https://www.httpbin.org/get?name=germy&age=25"
}
```

### 抓取网页

```python
import requests
import re
	
#返回的是Response
r = requests.get('https://ssr1.scrape.center/')
pattern = re.compile('<h2.*?>(.*?)</h2>',re.S)
#findall(pattern,string,flags=0)
titles = re.findall(pattern,r.text)
print(titles) 
```

  ==结果==

```python
 ['霸王别姬 - Farewell My Concubine', '这个杀手不太冷 - Léon', '肖申克的救赎 - The Shawshank Redemption', '泰坦尼克号 - Titanic', '罗马假日 - Roman Holiday', '唐伯虎点秋香 - Flirting Scholar', '乱世佳人 - Gone with the Wind', '喜剧之王 - The King of Comedy', '楚门的世界 - The Truman Show', '狮子王 - The Lion King']
```

### 二进制方式下载文件

```python
import requests

r = requests.get('https://scrape.center/facvicon.ico')

with open('facvicon.ico', 'wb') as f:
    f.write(r.content)
```

### 添加请求头

```python
 import requests

headers = {
    'User-Agent': 'Mozilla/5.0(WindowsNT10.0;Win64;x64)AppleWebKit/537.36(KHTML,likeGecko)Chrome/109.0.0.0Safari/537.36'
}

r = requests.get('https://scrape.center', headers=headers)
print(r.text)
```

### POST请求

​	通过post方法，data参数去上传数据

```python
import requests

data = {
    'name': 'germy',
    'age': 25
}
# 通过post方法，data参数去上传数据
r = requests.post('https://www.httpbin.org/post', data=data)
print(r.text)

# 通过调用status_code获取HTTP状态码
print(r.status_code)
```

  ==form部分就是提交数据的部分==

```python
 {
    "args": {},
    "data": "",
    "files": {},
    "form": {
        "age": "25",
        "name": "germy"
    },
    "headers": {
        "Accept": "*/*",
        "Accept-Encoding": "gzip, deflate",
        "Content-Length": "17",
        "Content-Type": "application/x-www-form-urlencoded",
        "Host": "www.httpbin.org",
        "User-Agent": "python-requests/2.20.0",
        "X-Amzn-Trace-Id": "Root=1-63f4d675-2dd392aa051f84c377ed6e55"
    },
    "json": null,
    "origin": "113.118.81.169",
    "url": "https://www.httpbin.org/post"
}
```

### 文件上传

响应中包含files和form两个字段，而form字段是空的，这证明文件上传部分会单独使用files字段

```python
import requests

files = {
    'file': open('favicon.ico', 'rb')
}

r = requests.post('https://www.httpbin.org/post', files=files)
print(r.text)

```

==结果==

```python
   `{`  ` "args": {}, `  ` "data": "", `  ` "files": {`  `  "file":  "data:application/octet-stream;base64,AAABAAEAICAAAAEAIACoEAAAFgAAACgAAAAgAAAAQAAAAAEAIAAAAAAAABAAABILAAASCwAAAAAAAAAAAABXP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1hA6/9aQuv/WkLr/1pC6/9aQuv/WkLr/1pC6/9aQuv/WkLr/1pC6/9aQuv/WkLr/1pC6/9aQuv/WkLr/1pC6/9aQuv/WkLr/1pC6/9aQuv/WkLr/1pC6/9aQuv/WEDr/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9YQOv/Ujrq/0ox6v9LMur/SzLq/0sy6v9LMur/SzLq/0sy6v9LMur/SzLq/0sy6v9LMur/SzLq/0sy6v9LMur/SzLq/0sy6v9LMur/SzLq/0sy6v9LMur/SzLq/0ox6v9SOur/WEDr/1c/6/9XP+v/Vz/r/1c/6/9XP+v/WEDr/1I66v9yXe7/n5H0/5qK8/+bi/P/m4vz/5uL8/+bi/P/m4vz/5uL8/+bi/P/m4vz/5uL8/+bi/P/m4vz/5uL8/+bi/P/m4vz/5uL8/+bi/P/m4vz/5uL8/+ZivP/n5H0/3Jd7v9SOur/WEDr/1c/6/9XP+v/Vz/r/1c/6/9aQuv/SzLq/5qM8////////Pz///////////////////////////////////////////////////////////////////////////////////////////////////z8////////mozz/0sy6v9aQuv/Vz/r/1c/6/9XP+v/Vz/r/1pC6/9LMur/mYrz///////6+f7//Pz///z8///8/P///Pz///z8///8/P///Pz///z8///8/P///Pz///z8///8/P///Pz///z8///8/P///Pz///z8///8/P//+vn+//////+aivP/SzLq/1pC6/9XP+v/Vz/r/1c/6/9XP+v/WkLr/0sy6v+ajPP///////z8//////////////7+///+/v///v7///7+///+/v///v7///7+///+/v///v7///7+///+/v///v7///7+///+/v/////////////8/P///////5uL8/9LMur/WkLr/1c/6/9XP+v/Vz/r/1c/6/9aQuv/SzLq/5qM8////////Pz///////////////////////////////////////////////////////////////////////////////////////////////////z8////////m4vz/0sy6v9aQuv/Vz/r/1c/6/9XP+v/Vz/r/1pC6/9LMur/mozz///////8/P///v7///////+VhfL/dF/v/3tn7/96Zu//embv/3pm7/96Zu//embv/3pm7/96Zu//embv/3tn7/90X+7/lYXz///////+/v///Pz///////+bi/P/SzLq/1pC6/9XP+v/Vz/r/1c/6/9XP+v/WkLr/0sy6v+ajPP///////z8///+/v///////3Rg7v9HLen/UTjq/0826v9PNur/Tzbq/0826v9PNur/Tzbq/0826v9PNur/UTjq/0cu6f90X+7///////7+///8/P///////5uL8/9LMur/WkLr/1c/6/9XP+v/Vz/r/1c/6/9aQuv/SzLq/5qM8////////Pz///7+////////e2jv/1E46v9aQ+v/WUHr/1lC6/9bROz/W0Ts/1tE7P9bROz/W0Ts/1tE7P9dRuz/VDzr/31q8P///////v7///z8////////m4vz/0sy6v9aQuv/Vz/r/1c/6/9XP+v/Vz/r/1pC6/9LMur/mozz///////8/P///v7///////96Zu7/Tzbq/1lB6/9YQOv/VDzr/0sy6v9LMur/SzLq/0sy6v9LMur/SzLq/0006v9DKen/cVzu///////+/v///Pz///////+bi/P/SzLq/1pC6/9XP+v/Vz/r/1c/6/9XP+v/WkLr/0sy6v+ajPP///////z8///+/v///////3pm7v9PNur/WULr/1Q76/9mT+v/morz/5iI8/+YiPP/mIjz/5iI8/+YiPP/mYn0/5SD8/+toPb////////////8/P///////5uL8/9LMur/WkLr/1c/6/9XP+v/Vz/r/1c/6/9aQuv/SzLq/5qM8////////Pz///7+////////embu/0826v9aQ+v/Tzbr/3xq6f////7//v7///////////////////////////////////////////////////z8////////m4vz/0sy6v9aQuv/Vz/r/1c/6/9XP+v/Vz/r/1pC6/9LMur/mozz///////8/P///v7///////96Zu7/Tzbq/1pD6/9PNuv/e2rq/////v/7+////Pz///z8///8/P///Pz///z8///8/P///f3//////////////Pz///////+ai/P/SzLq/1pC6/9XP+v/Vz/r/1c/6/9XP+v/WkLr/0sy6v+ajPP///////z8///+/v///////3pm7v9PNur/WkPr/0826/98aur////+//39///+/v///v7///7+///+/v///v7///7+///+/v///v7///7+///7+////////5qL8/9LMur/WkLr/1c/6/9XP+v/Vz/r/1c/6/9aQuv/SzLq/5qM8////////Pz///7+////////embu/0826v9aQ+v/Tzbr/3xq6f////7//v7//////v////7////+/////v////7////+/////v////7////+//z8/v//////m4zz/0sy6v9aQuv/Vz/r/1c/6/9XP+v/Vz/r/1pC6/9LMur/mozz///////8/P///v7///////96Zu7/Tzbq/1lB6/9VPev/X0np/31s6f98aur/fGrq/3xq6v98aur/fGrq/3xq6v98aur/fGrq/3xq6v98aur/e2rq/39t6v9mUOr/VDzr/1hA6/9XP+v/Vz/r/1c/6/9XP+v/WkLr/0sy6v+ajPP///////z8///+/v///////3pm7v9PNur/WUHr/1c/6/9VPev/TzXr/0826/9PNuv/Tzbr/0826/9PNuv/Tzbr/0826/9PNuv/Tzbr/0826/9PNuv/TjXr/1Q76/9YQOv/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9aQuv/SzLq/5qM8////////Pz///7+////////e2jv/1E46v9aQ+v/WUHr/1lB6/9bQ+v/WkPr/1pD6/9aQ+v/WkPr/1pD6/9aQ+v/WkPr/1pD6/9aQ+v/WkPr/1pD6/9bQ+v/WEHr/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1pC6/9LMur/mozz///////8/P///v7///////90YO7/SC3p/1E46v9PNur/Tzbq/0826v9PNur/Tzbq/0826v9PNur/Tzbq/0826v9PNur/Tzbq/0826v9PNur/UDfq/0826v9UPOv/WEDr/1c/6/9XP+v/Vz/r/1c/6/9XP+v/WkLr/0sy6v+ajPP///////z8///+/v///////5WF8v9zYO7/e2jv/3pm7/96Zu//embv/3pm7/96Zu//embv/3pm7/96Zu//embv/3pm7/96Zu//embv/3pm7/95Zu//fGnv/2RP7f9VPOv/WEDr/1c/6/9XP+v/Vz/r/1c/6/9aQuv/SzLq/5qM8////////Pz///////////////////////////////////////////////////////////////////////////////////////////////////z8////////mozz/0sy6v9aQuv/Vz/r/1c/6/9XP+v/Vz/r/1pC6/9LMur/mozz///////8/P/////////////+/v///v7///7+///+/v///v7///7+///+/v///v7///7+///+/v///v7///7+///+/v///v7///7+///+/v///Pv///////+ai/P/SzLq/1pC6/9XP+v/Vz/r/1c/6/9XP+v/WkLr/0sy6v+ZivP///////r5/v/8/P///Pz///z8///8/P///Pz///z8///8/P///Pz///z8///8/P///Pz///z8///8/P///Pz///z8///8/P///Pz///z8///6+f7//////5mK8/9LMur/WkLr/1c/6/9XP+v/Vz/r/1c/6/9aQuv/SzLq/5qM8////////Pz///////////////////////////////////////////////////////////////////////////////////////////////////z8////////m4zz/0sy6v9aQuv/Vz/r/1c/6/9XP+v/Vz/r/1hA6/9SOur/cl7u/5+R9P+ZivP/mozz/5qM8/+ajPP/mozz/5qM8/+ajPP/mozz/5qM8/+ajPP/mozz/5qM8/+ajPP/mozz/5qM8/+ajPP/mozz/5qM8/+ajPP/mYrz/5+R9P9yXe7/Ujrq/1hA6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1hA6/9SOur/SjHq/0sy6v9LMur/SzLq/0sy6v9LMur/SzLq/0sy6v9LMur/SzLq/0sy6v9LMur/SzLq/0sy6v9LMur/SzLq/0sy6v9LMur/SzLq/0sy6v9LMur/SjHq/1I66v9YQOv/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1hA6/9aQuv/WkLr/1pC6/9aQuv/WkLr/1pC6/9aQuv/WkLr/1pC6/9aQuv/WkLr/1pC6/9aQuv/WkLr/1pC6/9aQuv/WkLr/1pC6/9aQuv/WkLr/1pC6/9aQuv/WEDr/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/Vz/r/1c/6/9XP+v/AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA="`  ` }, `  ` "form": {}, `  ` "headers": {`  `  "Accept": "*/*", `  `  "Accept-Encoding": "gzip,  deflate", `  `  "Content-Length":  "4433", `  `  "Content-Type":  "multipart/form-data; boundary=c9c8ed065b546de3e2282b27af0a0032", `  `  "Host": "www.httpbin.org", `  `  "User-Agent":  "python-requests/2.20.0", `  `  "X-Amzn-Trace-Id":  "Root=1-63f4d742-1370f98522e972f536891082"`  ` }, `  ` "json": null, `  ` "origin":  "113.118.81.169", `  ` "url": "https://www.httpbin.org/post"`  `}`  
```

### cookie 设置

通过调用items()方法将Cookie转化为由元组组成的列表，遍历输出每一个Cookie条目的名称和值，实现对Cookie的遍历解析

```python
import requests

r = requests.get('http://www.baidu.com')
print(r.cookies)
print(r.cookies.items())

for key, value in r.cookies.items():
    print(key + '=' + value)

```

  ==结果==

```python
 <RequestsCookieJar[<Cookie BDORZ=27315 for .baidu.com/>]>
[('BDORZ', '27315')]
BDORZ=27315
```

### cookie模拟用户登录

通过头部带上 cookie 值登录网站（cookie可通过浏览器获取，但是需要注意 URL 和 cookie 的一致性）
_结果：成功登录并获取 51cto 用户名_

```python
import requests
import urllib.request

headers = {
    'Cookie': "aliyungf_tc=d9497faf5b2335c472890ac673d28f970741f30eddd296c7fe3c80224594b776;acw_tc=2f6fc11c16768787423513805e531a37ec18bca5e6bf4daad431eaba9146b3;_bl_uid=y6laje0ecdti1vb747yF4kq3k9h3;reg_sources=edu;pageIndexLoaded=1;pageLoaded=%2Findex;_uab_collina=167687875006914647236268;teducookienewactivity_type_cache_s_s1_699=yes;www51cto=A97AB438D698A053AFE9ADFEE1583512FnZc;sajssdk_2015_cross_new_user=1;login_from=edu.51cto.com;reg_from=edu.51cto.com;once_p=e6bad6;pub_sauth1=FhwMAQYAAlYEBVMIWwwEPQUHDFVRAgQGa1IBUwNcVQxWUww;pub_sauth2=6df6699933e6dc8ae9ad86d0576d4ec8;pub_cookietime=2592000;pub_auth_profile=Pf6u6bIEGuP%2B%2FvbA0ZNIFefiQ3go72uTn94je9FIdbg%3D;pub_wechatopen=aG0wVVNSAVoEDAUGVg;PHPSESSID=5pmhseqlbb5k04it761ujtefqq;Hm_lvt_8c8abdb71d78d33dfdb885e0bc71dae0=1676878777;sensorsdata2015jssdkcross=%7B%22distinct_id%22%3A%2214817017%22%2C%22first_id%22%3A%221866dc35130244-0b61df3e02bb8f-26021051-1440000-1866dc35131541%22%2C%22props%22%3A%7B%7D%2C%22%24device_id%22%3A%221866dc35130244-0b61df3e02bb8f-26021051-1440000-1866dc35131541%22%7D;_csrf=ab8304548ee3aeb230eccc12937f95daf10b13932ea8389b9f7f57eeed32db52a%3A2%3A%7Bi%3A0%3Bs%3A5%3A%22_csrf%22%3Bi%3A1%3Bs%3A32%3A%22%13%21%A1%00%8D%CF%AD%B54e%18%3A%ED%5E%8B%B5%15%15%5D%0D%13%036%87%A6%FB%DD%15%8F%24%93%CE%22%3B%7D;Hm_lpvt_8c8abdb71d78d33dfdb885e0bc71dae0=1676878813;ssxmod_itna=YqGxyDuDgDcnDtD8DzPx2Q6DkQSvgg28BxhOb3qGNLTDZDiqAPGhDC8btSh7f7GxhNf4+YwK4eWEYE3tNWxbh3pZ0DB3DEx0=eqi=xii9DCeDIDWeDiDG4Gm94GtDpxG=DjAKtZSjxYPDEAKDYxDrfrKDRxi7DD5QOx07DQv8QGDxfa7FotHm2hihF7h8D7ymDlPqAIOwEU3xga7Aw4A3mx0kB40Oc=vzkPoDUn9z9xANrARDWnTNQ044FlG4riD48i2Pk5rcNA2e3i+P3A0qq7eKF=xxDDaojvhDD==;ssxmod_itna2=YqGxyDuDgDcnDtD8DzPx2Q6DkQSvgg28BxhOb4A6WbKKD/7Q7DFr2GUPFh9+AyRLxlKG09dTgjCCi64rAExxhGD8hhIzljDLfKAF9bBacYKg9d4OCqAZBcRfdsQUFc9Kzp6Sg2/AaK=gZvHUxHkP/IPBmAiMO2b3DyqDRukPDYtghiCF45wclICioyYn00qPrKqVegfVlTzlpaLFhik2bKNRPPOK2yrVCvGXYlbd3e=g8lrdZup3kBXBPqG5NK6Z4EcK+KX38vLdK1z7/QbTkAgZWtjfHTUy6vxcFi2rLIHKVT/+76LHhrWbFQGWOrWQYtirW8ihKfQ4L=b+Nm43R3492Y+8DQbwmOD2oWmOqDf=+R4Wf3BBm4uwfeiKO+Mrmx47RSHdnHpj58ivx8+YRpmRYeeigjiHghalhxZhV8pyMkwCvXhOaPIK+Ogo+Z7Q6m7CZxEDDw=KDjKDewD4D==="
}

r = requests.get('https://edu.51cto.com/center/course/user/get-study-course')
# print(r.status_code)
# print(r.text)

data = urllib.request.urlopen(
    'https://edu.51cto.com/center/course/user/get-study-course')

# print(data.geturl())
print(data.read().decode("UTF-8", 'ignore'))
```

### session 维持

在我们使用 POST 方法带上 cookie 成功登录之后，再使用 GET 请求希望获取用户信息的时候，是会失败的！
原因是：两个不同的请求相当于打开了两个浏览器，是两个独立的操作，对应不同的 Session，除非我们在每个请求的头部带上 Cookie 信息，但是这样会非常繁琐
解决方案是：维持同一个 Session，使用一个会话，那么在请求下一个网页时打开的是下一个浏览器选项卡而非浏览器 

```python
import requests

s = requests.session()
s.get('https://www.httpbin.org/cookies/set/number/123456789')
r = s.get('https://www.httpbin.org/cookies')
print(r.text)
```

 ==结果==

```python
{
	"cookies": {
	 "number": "123456789"
	}
}
```

### SSL 证书验证

在访问的网站出现如下页面时，则表示证书错误或者证书不被CA官方认可，此时无法获取资源

![img](data:image/png;base64,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AACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASSBsAAAAAACASbTQsFFD//vEeWJfOOj3gHcM7IcAAADAab1ixQq62ILkbfZ0D842f86hV7dnzFXylCT+o4kfxl2oumvVrWentm3o1vtwZstbKxLu1Vm1feYZy7Zt9GS3mjNbpiyLuVxj3q7j88+1pRuNI5MxbYx5qmc2uLtN33gg77JZ50FOnczp1sa6nLxqW/zRowWivgM6G3EfNZK8X1ctmf5T2zdedbCg2+ARcvNM/Jplk7+46v72kI73d24B+yEAAAA0Vqv6+nq62GKUJvqOWnpIyjBi76g/g0YKC/anN7hPXFXMMObvbDn5xYimFoMESLnqNfYOGeegtDhvO36jVsbfUkhyZtfKj4MyX41Lmt/XUDkvb+PI18IvkwWXLw7FvdOV39h4OaE93trCMCO/PhL1pg3dpseZDaNfW1XAMHa+0QcC3ZrhLTWdov3f/JRHl++Dzf/NmDbYkq40VpVEUmtuaanjfbqw47XRK8v69BzkPHNh4Pi+zfBuSpI/GDovUUaWjP1AtcJ+CAAAAE3S8sKG7NCng3x/IKUr84nrjqweJywUlu/yHbo8lSw4Bx/YPa0nv/G+5K0d+doqtlzluSZr43h9BdAmlcAkhwKG+sayJUWH2bt/We6stwh1Zp37xG/YRxizOmv9xA78xiZoZCGv4Q1vtnfVVI6vfP6tbXT5Phh+Z2QSiZSR3bhw/urd8qKjBWWy4pyMghvlBWfK2c+S8Y7KDxqp7bOU7Fs6YGEiuzQi9FjUpKYmA1UXdniNDs4mC11nxB1Y7tKU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)

 

此时可以使用 ```verify```参数不验证SSL证书，但是会出现警告信息，此时我们可以使用``` urllib3.disable_warnings()```方法屏蔽SSL证书警告
verify 参数控制是否验证证书，默认为 TRUE，表示自动验证，当值为FALSE时，表示不会再验证证书是否有效

```python
import requests
import urllib3

# urllib3.disable_warnings()方法屏蔽SSL证书警告
urllib3.disable_warnings()

# verify参数不验证SSL证书
response = requests.get('https://ssr2.scrape.center/', verify=False)
print(response.status_code)

```



也可以通过指定证书的方式访问，具体查看p60

### 超时设置

在网络状态不良好的清空下，可能会出现访问时间太慢甚至无响应的清空，此时可以设置 timeout 属性解决此问题，

请求分为两个阶段，分别是：连接（connect）和读取（read），而timeout则是这两个阶段的总和（最终时间）也可以通过元组的方式分别控制时间

```python
r1=requests.get('https://www.httpbin.org/get',timeout=(1,30))
```

  

如果想永久等待，也可以将 ``值`` 设置为 `timeout`

```python
r2=requests.get('https://www.httpbin.org/get',timeout=None)
```

### 基本身份验证

```python
import requests
from requests.auth import HTTPBasicAuth

request = requests.get('http://61.147.171.105:52838/', auth=HTTPBasicAuth('admin', 'admin'))
print(request.content)
```

## 2.2 正则表达式

### 常用的匹配规则

```python
	正则说明：
	\A:表示从字符串的开始处匹配
	\Z:表示从字符串的结束处匹配，如果存在换行，只匹配到换行前的结束字符
	\b:匹配一个单词边界，也就是指单词和空格的位置。例如：'py\b'可以匹配“python”中的'py'，但不能匹配’openpyx1'中的‘py’
	\B:匹配非单词边界。'py\b'可以匹配“openpyx1"中的'py'，但不能匹配”python“中的'py'
	\d:匹配任意数字，等价于[0-9]
	\D：匹配任意非数据字符，等价于[^\d]
	\s：匹配任意空白字符，等价于[\t\n\r\f]
	\S：匹配任意非空白字符
	\w:匹配任意字母数字及下划线，等价于[a-zA-Z0-9]
	\W：匹配任意非字母数字及下划线，的等价于[^\w]
	\Z：匹配字符串结尾。如果存在换行，只匹配到换行前的结束字符串
	\z：匹配字符串结尾。如果存在换行，同时还会匹配换行符
	\G：匹配最后匹配完成的位置
	\n：匹配一个换行符
	\t：匹配一个制表符
	\\:匹配原义的反斜杠\
	
	‘.’用于匹配除换行符(\n)之外的所有字符
	'^'用于匹配字符串的开始，即首行
	'$'用于匹配字符串的末尾（末尾如果换行符\n，就匹配\n前面的那个字符），即行尾
	[…]：用来匹配一组字符，单独列出，例如[amk]用来匹配a、m、k
	[^…]：匹配不再[]中的字符，例如匹配除了a、b、c之外的字符
	
	
	定义正则验证次数：
	'*'用于将前面的模式匹配0次或多次(贪婪模式，即尽可能多的匹配)>=0
	'+'用于将前面的模式匹配1次或多次（贪婪匹配）>=1
	'?'用于将前面的模式匹配0次或1次(贪婪匹配)0，1
	'{m}'用于验证将前面的模式匹配m次
	{m,}'用于验证将前面的模式匹配m次或者多次>=m
	'*?,+?,??'即上面三种特殊字符的非贪婪模式（尽可能少的匹配）
	'{m,n}'用于将前面的模式匹配m次到n次（贪婪模式），即最小匹配m次，最大匹配n次
	'{m,n}?'即上面‘{m,n}’的非贪婪版本
	
	'\\'：‘\’是转移字符，在特殊字符前面在上\，特殊字符就失去了其所代表顶顶顶含义，比如\+就仅仅代表+号本身
	'[]'用于标识一组字符，如果^是第一个字符，则标识的是一个补集。比如[0-9]表示所有的数字，[^0-9]表示除了数字外的字符
	'|'比如A|B用于匹配A或B
	'(...)'用于匹配括号中的模式，可以在字符串中检索或匹配我们所需要的内容
	
	import re
	
	'''
	string='''1234987taoyunjiaoyu
	baidu
	'''
	pat='\d\d\d\d\w'
	rst=re.search(pat,string)
	print(rst)

```

#### match

>   match 方法会从字符串的起始位置开始匹配正则表达式，如果匹配，就返回匹配成功的结果；如果不匹配，就返回None



#### 通用匹配

>   .可以匹配任意字符（除换行符），*代表匹配前面的字符无限次，所以它们组合在一起就可以匹配任意字符了



#### 贪婪与非贪婪匹配

>   使用非贪婪匹配“?”会为后面的正则参数留下最多的匹配字符
>   如果匹配的结果在字符串结尾，.*?有可能匹配不到任何内容了，因为它会匹配尽可能少的字符

```python
import re

content = 'http://weibo.com/comment/kEraCN'
result1 = re.match('http.*?comment/(.*?)', content)
result2 = re.match('http.*?comment/(.*)', content)

print('result：', result1.group(1))
print('result：', result2.group(1))
```

==结果==

```python
result： 
result： kEraCN
```

### 修饰符

修饰符可以用来控制正则表达式的匹配模式

 ```python
"""
模式修正符：
re.I：匹配时忽略大小写*
re.M：多行匹配*
re.L：本地化识别匹配
re.U：Unicode解析
re.S：让.匹配包括换行*
re.X：该标志能够给予你更灵活的格式，以便将这种表达式书写得更易于理解
"""
importre

string = """Python
123
"""
pat = '.+'

rst = re.search(pat, string, re.S)
print(rst)
 ```

### 转义匹配

当在目标字符串中遇到用正则匹配模式的特殊字符时，在此字符前面加反斜杠\转义一下即可

```python
import re

content = '(百度)www.baidu.com'
result = re.match('\(百度\)www.baidu.com', content)
print(result)
```

### search

match方法是从字符串的开头开始匹配的，意味着一旦开头不匹配，整个匹配就会失败

**_此时有第二个匹配方法：search_**

>   原理：
>   search方法会扫描整个字符串，然后返回第一个匹配成功的结果，也就是说，search方法会依次以每个字母作为开头扫描字符串，知道找到第一个符合规则的字符串，然后返回匹配内容；如果扫描完还没有找到符合规则的字符串，就返回None

```py
import re

content = 'Extastringshello1234567world_thisisaregexdemoextrastring'
result = re.search('hello.*?(\d+).*?demo', content)
print(result)
```

### findall 方法

第三个匹配方法：findall
**search和findall的区别是：**search只会匹配第一个符合结果的字符串，而findall会匹配所有符合结果的字符串

### sub 方法

有时候我们需要对字符串进行字符替换，此时如果使用字符串的replace方法，会太麻烦，此时可以直接使用sub方法替

```py
import re

content = 'f4e8s94ad98f4s89ef7489sdf'
content = re.sub('\d+', '', content)
print(content)
```

### compile 方法

complie方法可以将正则字符串变异成正则表达式对象，以便后续的匹配中重复使用

```py
import re

content1 = '2019-12-1512:00'
content2 = '2019-12-1812:88'
content3 = '2019-12-2612:08'

pattern = re.compile('\d{2}:\d{2}')
print(pattern)
print(type(pattern))

res1 = re.sub(pattern, '', content1)
res2 = re.sub(pattern, '', content2)
res3 = re.sub(pattern, '', content3)
```

## 2.4 httpx的使用（爬取HTTP 2.0）

>HTTPX 的安装方式如下：pip install http[http2]

### 实例

requests 和 urllib 都只可以爬取 HTTP 1.X 的版本，我们可以通过查看 network 面板证明请求的协议是否使用 HTTP2.0

![image-20230509180818153](1.%20Python%20%E7%BD%91%E7%BB%9C%E7%BC%96%E7%A8%8B%E5%BC%80%E5%8F%91%E5%AE%9E%E6%88%98%20-%20%E7%AC%AC%E4%B8%80%E7%AB%A0.assets/image-20230509180818153.png)



若强行使用 requests 爬取，则会出现以下错误

```py
import requests

url = 'https://spa16.scrape.center/'
response = requests.get(url)
print(response.text)
```

==结果==

```py
requests.exceptions.ConnectionError: ('Connection aborted.', RemoteDisconnected('Remote end closed connection without response'))
```

### 基本使用

httpx 和 requests 的很多API存在相似之处，使用的方法也大致相同

```py
import httpx

response = httpx.get('https://www.httpbin.org/get')
print(response.status_code)
print(response.headers)
print(response.text)
```

==结果==

```py
200
Headers({'date': 'Thu, 23 Feb 2023 14:40:41 GMT', 'content-type': 'application/json', 'content-length': '314',
         'connection': 'keep-alive', 'server': 'gunicorn/19.9.0', 'access-control-allow-origin': '*',
         'access-control-allow-credentials': 'true'})
{
    "args": {},
    "headers": {
        "Accept": "*/*",
        "Accept-Encoding": "gzip, deflate",
        "Host": "www.httpbin.org",
        "User-Agent": "python-httpx/0.23.3",
        "X-Amzn-Trace-Id": "Root=1-63f77ae6-7ac29a3642e980d207dfd91c"
    },
    "origin": "220.246.107.182",
    "url": "https://www.httpbin.org/get"
}

```

**若需要在头部加入 User-Agent，可这样写**

```py
import httpx

headers = {
    'User-Agent': "Mozilla/5.0(WindowsNT6.1;WOW64)AppleWebKit/535.11(KHTML,likeGecko)Chrome/17.0.963.56Safari/535.11"
}

response = httpx.get('https://www.httpbin.org/get', headers=headers)
print(response.text)

```

==结果==

```py
{
    "args": {},
    "headers": {
        "Accept": "*/*",
        "Accept-Encoding": "gzip, deflate",
        "Host": "www.httpbin.org",
        "User-Agent": "Mozilla/5.0(WindowsNT6.1;WOW64)AppleWebKit/535.11(KHTML,likeGecko)Chrome/17.0.963.56Safari/535.11",
        "X-Amzn-Trace-Id": "Root=1-63f77baf-7207fa181a0db7165a845186"
    },
    "origin": "113.118.82.88",
    "url": "https://www.httpbin.org/get"
}
```

如果希望使用 httpx 访问 HTTP/2.0，则需要手动声明（默认使用的时 HTTP/1.1）
同时我们也可以使用 http_version 属性来查看当前所使用的版本

```py
import httpx

client = httpx.Client(http2=True)
response = client.get('https://spa16.scrape.center/')
print(response.text)
print(response.http_version)
```

==结果==

```html
<!DOCTYPE html><html lang=en><head><meta charset=utf-8><meta http-equiv=X-UA-Compatible content="IE=edge"><meta name=viewport content="width=device-width,initial-scale=1"><meta name=referrer content=no-referrer><link rel=icon href=/favicon.ico><title>Scrape | Book</title><link href=/css/chunk-50522e84.e4e1dae6.css rel=prefetch><link href=/css/chunk-f52d396c.4f574d24.css rel=prefetch><link href=/js/chunk-50522e84.6b3e24aa.js rel=prefetch><link href=/js/chunk-f52d396c.f8f41620.js rel=prefetch><link href=/css/app.ea9d802a.css rel=preload as=style><link href=/js/app.b93891e2.js rel=preload as=script><link href=/js/chunk-vendors.a02ff921.js rel=preload as=script><link href=/css/app.ea9d802a.css rel=stylesheet></head><body><noscript><strong>We're sorry but portal doesn't work properly without JavaScript enabled. Please enable it to continue.</strong></noscript><div id=app></div><script src=/js/chunk-vendors.a02ff921.js></script><script src=/js/app.b93891e2.js></script></body></html>
```

### Client 对象

官方推荐使用 with as 语句构建请求

```py
import httpx

with httpx.Client() as client:
    response = client.get('https://www.httpbin.org/get')
    print(response)
```

```
<Response [200 OK]>
```

这个用法等价于

```py
import httpx

client = httpx.Client()

try:
    response = client.get('https://www.httpbin.org/get')
finally:
    client.close()
```

两者区别在于：第二种方法需要使用 close方法 来关闭Client（类似于文件操作）

# 第三章：网页数据的解析提取

## 3.1 XPath 的使用

### XPath 常用规则

| 表达式   | 描述                     |
| -------- | ------------------------ |
| nodename | 选取此节点的所有子节点   |
| /        | 从当前节点选择直接子节点 |
| //       | 从当前节点选择子孙节点   |
| .        | 选取当前节点             |
| ..       | 选取当前节点的父节点     |
| @        | 选取属性                 |

 

**注意：当“/”和“//”在开头使用时：**

+   “/”表示根节点

+   “//”表示任意节点

 

### 实例引入

**要使用****XPath****表达式检索文件，必须先将对象转换为****ElementTree****对象**

**XPath** **表达式返回的是一个列表，所以我们使用切片来获取需要的数据**

#### 直接过滤 字符串

```py
from lxml import etree

test = """
    <div>
        <ul>
            <li class="item-0"><a href="link1.html">first item</a></li>
            <li class="item-1"><a href="link2.html">Second item</a></li>
            <li class="item-inactive"><a href="link3.html">third item</a></li>
            <li class="item-1"><a href="link14.html">fourth item</a></li>
            <li class="item-0"><a href="link1.html">fifth item</a></li>
        </ul>
    </div>
"""

# 将字符串转换为 ElementTree 对象
html = etree.HTML(test)
# 将 ElementTree 转换为HTML文本，并使用 ‘utf-8’ 来解码
result = etree.tostring(html).decode('utf-8')
print(result)
```

#### 过滤文件

```py
from lxml import etree

# 过滤文件，第一个参数是 文件名， 第二个参数是 解析器
html = etree.parse('test.html', etree.HTMLParser())
# 将 ElementTree 转换为HTML文本，并使用 ‘utf-8’ 来解码
result = etree.tostring(html).decode('utf-8')
print(result)
```

#### 抓取所有节点

```py
from lxml import etree

test = """
    <div>
        <ul>
            <li class="item-0"><a href="link1.html">first item</a></li>
            <li class="item-1"><a href="link2.html">Second item</a></li>
            <li class="item-inactive"><a href="link3.html">third item</a></li>
            <li class="item-1"><a href="link14.html">fourth item</a></li>
            <li class="item-0"><a href="link1.html">fifth item</a></li>
        </ul>
    </div>
"""

# 将字符串转换为 ElementTree 对象
html = etree.HTML(test)
# 获取 ElementTree 中的所有 Element
result = html.xpath('//*')
print(result)
```

#### 抓取 ElementTree 中的所有的 li 节点

```py
from lxml import etree

test = """
    <div>
        <ul>
            <li class="item-0"><a href="link1.html">first item</a></li>
            <li class="item-1"><a href="link2.html">Second item</a></li>
            <li class="item-inactive"><a href="link3.html">third item</a></li>
            <li class="item-1"><a href="link14.html">fourth item</a></li>
            <li class="item-0"><a href="link1.html">fifth item</a></li>
        </ul>
    </div>
"""

# 将字符串转换为 ElementTree 对象
html = etree.HTML(test)

result = html.xpath('//li')
print(result)
```

#### 抓取 li节点 下的 a节点

```py
from lxml import etree

test = """
    <div>
        <ul>
            <li class="item-0"><a href="link1.html">first item</a></li>
            <li class="item-1"><a href="link2.html">Second item</a></li>
            <li class="item-inactive"><a href="link3.html">third item</a></li>
            <li class="item-1"><a href="link14.html">fourth item</a></li>
            <li class="item-0"><a href="link1.html">fifth item</a></li>
        </ul>
    </div>
"""

# 将字符串转换为 ElementTree 对象
html = etree.HTML(test)

result = html.xpath('//li/a')
print(result)
```

### 父节点

#### 通过子元素抓取父节点

```py
from lxml import etree

test = """
    <div>
        <ul>
            <li class="item-0"><a href="link1.html">first item</a></li>
            <li class="item-1"><a href="link2.html">Second item</a></li>
            <li class="item-inactive"><a href="link3.html">third item</a></li>
            <li class="item-1"><a href="link14.html">fourth item</a></li>
            <li class="item-0"><a href="link1.html">fifth item</a></li>
        </ul>
    </div>
"""

# 将字符串转换为 ElementTree 对象
html = etree.HTML(test)
# 通过子节点获取父节点
result = html.xpath('//a[@href="link3.html"]/../@class')
print(result)
```

### 文本获取

text() 方法获取节点中的文本；text() 方法有可能会抓取到 换行符（\n）或者是空格

```py
from lxml import etree

test = """
    <div>
        <ul>
            <li class="item-0"><a href="link1.html">first item</a></li>
            <li class="item-1"><a href="link2.html">Second item</a></li>
            <li class="item-inactive"><a href="link3.html">third item</a></li>
            <li class="item-1"><a href="link14.html">fourth item</a></li>
            <li class="item-0"><a href="link1.html">fifth item</a></li>
        </ul>
    </div>
"""

# 将字符串转换为 ElementTree 对象
html = etree.HTML(test)

result = html.xpath('//a[@href="link3.html"]/text()')
print(result)
```

### 属性获取

可以直接通过 @ 来指定属性名

```py
from lxml import etree

test = """
    <div>
        <ul>
            <li class="item-0"><a href="link1.html">first item</a></li>
            <li class="item-1"><a href="link2.html">Second item</a></li>
            <li class="item-inactive"><a href="link3.html">third item</a></li>
            <li class="item-1"><a href="link14.html">fourth item</a></li>
            <li class="item-0"><a href="link1.html">fifth item</a></li>
        </ul>
    </div>
"""

# 将字符串转换为 ElementTree 对象
html = etree.HTML(test)

result = html.xpath('//li/a/@href')
print(result)
```

### 属性多值匹配

在下面情况下，class 有两个值，若需要匹配其中一个，则可以使用 contains 方法

 ` <li  class="li item-0"><a href="link1.html">first  item</a></li>  `

contains 方法语法：

+   参数一：属性名称

+   参数二：属性值

只需要传入的属性包含传入的属性值就可以完成匹配

```py
from lxml import etree

test = """
    <div>
        <ul>
            <li class="li item-0"><a href="link1.html">first item</a></li>
            <li class="item-1"><a href="link2.html">Second item</a></li>
            <li class="item-inactive"><a href="link3.html">third item</a></li>
            <li class="item-1"><a href="link14.html">fourth item</a></li>
            <li class="item-0"><a href="link1.html">fifth item</a></li>
        </ul>
    </div>
"""

# 将字符串转换为 ElementTree 对象
html = etree.HTML(test)

result = html.xpath('//li[contains(@class, "li")]/a/text()')
print(result)
```

### 多属性匹配

如果在XPath的过滤中，我们需要同时考虑两个属性，就必须使用 逻辑运算符 and

```py
from lxml import etree

test = """
    <div>
        <ul>
            <li class="li item-0" name="item"><a href="link1.html">first item</a></li>
            <li class="item-1"><a href="link2.html">Second item</a></li>
            <li class="item-inactive"><a href="link3.html">third item</a></li>
            <li class="item-1"><a href="link14.html">fourth item</a></li>
            <li class="item-0"><a href="link1.html">fifth item</a></li>
        </ul>
    </div>
"""

# 将字符串转换为 ElementTree 对象
html = etree.HTML(test)

result = html.xpath('//li[contains(@class, "li") and @name="item"]/a/text()')
print(result)
```

### XPath 表达式运算符

| **运算符** | **描述**       | **实例**                  | **返回值**                                                   |
| ---------- | -------------- | ------------------------- | ------------------------------------------------------------ |
| \|         | 计算两个节点集 | //book \| //cd            | 返回所有拥有 book 和 cd 元素的节点集                         |
| +          | 加法           | 6 + 4                     | 10                                                           |
| -          | 减法           | 6 - 4                     | 2                                                            |
| *          | 乘法           | 6 * 4                     | 24                                                           |
| div        | 除法           | 8 div 4                   | 2                                                            |
| =          | 等于           | price=9.80                | 如果 price 是 9.80，则返回 true。  如果 price 是 9.90，则返回 false。 |
| !=         | 不等于         | price!=9.80               | 如果 price 是 9.90，则返回 true。  如果 price 是 9.80，则返回 false。 |
| <          | 小于           | price<9.80                | 如果 price 是 9.00，则返回 true。  如果 price 是 9.90，则返回 false。 |
| <=         | 小于或等于     | price<=9.80               | 如果 price 是 9.00，则返回 true。  如果 price 是 9.90，则返回 false。 |
| >          | 大于           | price>9.80                | 如果 price 是 9.90，则返回 true。  如果 price 是 9.80，则返回 false。 |
| >=         | 大于或等于     | price>=9.80               | 如果 price 是 9.90，则返回 true。  如果 price 是 9.70，则返回 false。 |
| or         | 或             | price=9.80 or price=9.70  | 如果 price 是 9.80，则返回 true。  如果 price 是 9.50，则返回 false。 |
| and        | 与             | price>9.00 and price<9.90 | 如果 price 是 9.80，则返回 true。  如果 price 是 8.50，则返回 false。 |
| mod        | 计算除法的余数 | 5 mod 2                   | 1                                                            |

### 按序选择

在过滤多个 li 节点时，可以往中括号中传入索引方法来获取特定次序的节点

```py
from lxml import etree

test = """
    <div>
        <ul>
            <li class="li item-0" name="item"><a href="link1.html">first item</a></li>
            <li class="item-1"><a href="link2.html">Second item</a></li>
            <li class="item-inactive"><a href="link3.html">third item</a></li>
            <li class="item-1"><a href="link14.html">fourth item</a></li>
            <li class="item-0"><a href="link1.html">fifth item</a></li>
        </ul>
    </div>
"""

# 将字符串转换为 ElementTree 对象
html = etree.HTML(test)

# 抓取第一个 li 节点
result = html.xpath('//li[1]/a/text()')
print(result)
# 抓取最后一个 li 节点
result = html.xpath('//li[last()]/a/text()')
print(result)
# 抓取 位置 小于3的 li 节点
result = html.xpath('//li[position()<3]/a/text()')
print(result)
# 抓取 最后以为的位置 -2 的节点（倒数第三个）
result = html.xpath('//li[last()-2]/a/text()')
print(result)
```

## 3.2 Beautiful Soup的使用

>   首先这里先介绍一下 Beautiful Soup 解析器之一：LXML HTML 解析器：速度快、文档容错能力强 
>
>   Beautiful Soup 的核心作用：将所有 HTML 代码字符串按标准缩进格式输出

### 基本使用：

1、在实例化 Beautiful Soup 的时候，若遇到没有 正确闭合的 HTML代码， Beautiful Soup 还会自动更正
2、prettify() 方法：将要解析的字符串以标准的缩进形式输出

BeautifulSoup的实例化格式：
        soup = BeautifulSoup(目标字符串， 解析器)
        
Beautiful Soup 节点选择器：
        格式：soup.HTML标签名      或       soup.HTML标签名.string
        第一种：输出 标签 内的所有内容，包括 HTML 代码
        第二种：输出 标签 内的所有文本内容，不包括 HTML 代码
        

        此外：
                1、若选择的标签在 HTML代码 中同时存在多个（如 <li>、<p>、<a>），Beautiful Soup 默认只会选择第一个，其他都会被忽略
                2、Beautiful Soup 选择器还支持嵌套选择

Beautiful Soup 获取属性
        格式：
                soup.HTML标签名.attrs 
                soup.HTML标签名.attrs['属性名']
                

                第一种会以字典的形式返回标签内的所有属性和值
                第二种会以字符串的形式返回标签内的指定属性的值
                        此外：若查询的属性在 HTML 中是唯一的（id，name），则会直接以字符串的形式返回；若查询的属性在 HTML 中不唯一（class），则会以字典的形似和返回

Beautiful Soup 关联选择
        格式：
                soup.HTML标签名.HTML标签.关联选择器
               

                支持的关联选择器有：
                        children：选择所有子节点
                        descendants：选择所有子孙节点
                        parent：选择第一个父节点
                        parents：选择所有父节点（祖先节点）
                        next_sibling：返回下一个兄弟节点
                        next_siblings：返回后面的所有兄弟节点
                        previous_sibling：返回上一个兄弟节点 
                        previous_siblings：返回前面的所有兄弟节点
                        
        注意：关联选择返回的是 可迭代对象（enumerate ）需要使用 enumerate() 函数进行遍历；详情请看：P103-107

Beautiful Soup find_all() 方法：
        • name
        Beautiful Soup 还提供了额外的查询方，如：find_all， find
        格式：
                find_all(name, attrs, recurisve, text, **kwargs)
                
        我们可以使用 name 来查询，name="标签名"

Beautiful Soup CSS 选择器
        Beautiful Soup 还支持使用 CSS选择器 来选择元素的方法；且支持 嵌套选择
        格式：
                soup.select('div ul li input')
               

        若需要获取属性值，格式：
                for i in soup.select('div ul li input'):
                    print(i["id"])
                    print(i.attrs["id"])
                        
        若需要获取文本（getText-），格式：
                for i in soup.select('div ul li input'):
                    print(i.getText)


​	        

```py
import bs4

html = """
    <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>个人注册</title>
</head>
<body>
    <div class="w">
        <header>
<div class="logo"><a href="index.html"></a></div>
        </header>
        <div class="registerarea">
            <h3>
                注册新用户
<em>我有账号，去<a href="login.html">登陆</a></em>
            </h3>
            <div class="reg_form">
                <form action="php/register.php" method="post">
                    <ul>
                        <li>
                            <label for="code">短信验证码：</label>
                            <input type="text" class="inp" id="code" value="2e34">
                        <li class="safe">
                            安全程度 
                            <em class="ruo">弱</em>
                            <em class="zhong">中</em>
                            <em class="qiang">强</em>
                        </li>
                        <li class="agree">
                            <input type="checkbox" checked="checked">
                            同意协议并注册
                            <a href="#">《京东用户注册协议和隐私政策》</a>
                        </li>
                        <li>
                            <input type="submit" value="完成注册" class="over">
                        </li>
                    </ul>
                </form>
            </div>
        </div>
    </div>
</body>
"""

# 实例化 BeautifulSoup 类并传入参数
# 参数一：目标字符串
# 参数二：解析器的类型
soup = bs4.BeautifulSoup(html, 'lxml')
# prettify() 方法将要解析的字符串以标准缩进的方法输出
print(soup.prettify())

# ----------- 节点选择器 ------------
# 输出 目标字符串 的 title 中的内容，包括 HTML标签 代码
print(soup.title)
# 输出 目标字符串 的 title 中的 文本内容；不包括 HTML 标签代码
print(soup.title.string)

print(soup.li)
# 此外 Beautiful Soup 还支持多重标签选择器
print(soup.li.label.string)

# 经过 Beautiful Soup 选择的结果都是 bs4.element.Tag 类型
# <class 'bs4.element.Tag'>
print(type(soup.li))

# ---------- 获取属性 -------------
# 以字典的形式返回 标签 内的所有属性
print(soup.div.attrs)
# 以字符串的形式返回 标签 内的指定属性
print(soup.div.attrs['class'])
print(soup.div.attrs['id'])

# ----------- find_all() 方法-----------------
print('------------------------------------')
# 这里返回的是一个列表，所以如果希望嵌套查询，还需要进行二次遍历
print(soup.div.find_all(name="li"))

# ------------ CSS选择器 --------------------------
print('------------------------------------')
print(soup.select('div ul li input'))

for i in soup.select('div ul li input'):
    print(i.getText)

```

## 3.3 Parsel 的使用

### 基本使用

```py
from parsel import Selector

html = """
    <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>个人注册</title>
</head>
<body class="body">
    <div class="w" id="header">
        <header>
<div class="logo" name="logo"><a href="index.html"></a></div>
        </header>
        <div class="registerarea">
            <h3>
                注册新用户
<em>我有账号，去<a href="login.html">登陆</a></em>
            </h3>
            <div class="reg_form">
                <form action="php/register.php" method="post">
                    <ul>
                        <li>
                            <label for="code">短信验证码：</label>
                            <input type="text" class="inp" id="code" value="2e34">
                        <li class="safe" id="code>
                            安全程度 
                            <em class="ruo">弱</em>
                            <em class="zhong">中</em>
                            <em class="qiang">强</em>
                        </li>
                        <li class="agree" name="agree">
                            <input type="checkbox" checked="checked">
                            同意协议并注册
                            <a href="#">《京东用户注册协议和隐私政策》</a>
                        </li>
                        <li>
                            <input type="submit" value="完成注册" class="over">
                        </li>
                    </ul>
                </form>
            </div>
        </div>
    </div>
</body>
"""

# 实例化 Selector 对视并使用 text 参数传入目标字符串
selector = Selector(text=html)

# 使用 css 选择器选择元素
items = selector.css('.body')

# items 返回的是 Selector 构成的可迭代对象，遍历完成之后还是返回的是 Selector 对象，所以需要使用 get() 或 getall() 方法获取文本内容
for item in items:
    text = item.xpath('//li/text()').getall()
    print(text)

# 还可以使用这种方法提取属性
for item in items:
    text = item.xpath('//li/input/@type').getall()
    print(text)

# 我们还可以使用 正则 来进一步提取数据，由于 re 自动遍历了所有提取到的 Selector 对象，所以无需额外调用 Selector 方法（get()、getall()）
for item in items:
    text = item.xpath('//li/input/@type').re('text')
    print(text)

```

## 第四章：数据的存储

### 4.2 JSON文件存储

1、JSON 简介：JSON 说白了就是字符串。由于所有的 编程语言 都认识字符串，所以所有编程语言都可以解析 JSON

 

+   在 Python 中，可以将 JSON文本和JSON对象 相互转换；

+   在 Python中，JSON对象就是一个列表，列表里有多个字典：[{}, {}, {}]

+   在 Python中，JSON字符串的表示必须**全部都使用双引号**（字符串中不能出现单引号），否则 loads 方法回解析失败



2、loads方法 读取JSON字符串：我们可以使用 loads 方法将 JSON文本字符串 转为 JSON对象

```py
import json

str = """
[{
    "name": "Bob",
    "gender": "male",
    "birthday": "1992-10-18"
},
{
    "name": "Selina",
    "gender": "female",
    "birthday": "1995-10-181"
}]
"""

# 使用 loads 方法将 JSON文本字符串 转为 JSON对象
data = json.loads(str)
print(data)
```

3、load方法 读取JSON文件：load 方法的参数是一个文件操作对象，loads的参数是一个JSON字符串

```py
import json

data = json.load(open('input.json', encoding='utf-8'))
print(data)
```

JSON文件

```py
[{"name": "Bob", "gender": "male", "birthday": "1992-10-18"}, {"name": "Selina", "gender": "female","birthday": "1995-10-181"}]
```

4、dump 方法将 JSON 对象输出为 JSON文件

>   这里使用 indent=2 来保存缩进格式，使用 ensure_ascii=False 来保证中文能被成功转换（否则转为Unicode 编码）

```py
import json

data = [{
    "name": "鲍勃",
    "gender": "male",
    "birthday": "1992-10-18"
},
    {
        "name": "萨丽娜",
        "gender": "female",
        "birthday": "1995-10-181"
    }]


with open('data.json', 'w') as f:
    f.write(json.dumps(data, indent=2, ensure_ascii=False))
```

JSON文件

```json
	[
	  {
	    "name": "鲍勃",
	    "gender": "male",
	    "birthday": "1992-10-18"
	  },
	  {
	    "name": "萨丽娜",
	    "gender": "female",
	    "birthday": "1995-10-181"
	  }
	]
```

5、dumps 方法直接将 对象 输出为 JSON字符串

```py
import json

str = ['admin']
print(json.dumps(str))
print(type(json.dumps(str)))
```

### 4.3 CSV文件存储

#### 1、CSV简介：

CSV，全称为：Comma-Separated Values：文件以纯文本的形式存储表格数据，各条记录以某种换行符分隔开。每条记录都由若干个字段组成，字段间的分隔符时其他字符或字符串

#### 2、写入

1）可支持（多个）列表写入，也可支持（多个）字典写入

2）若值中有中文，则需要定义字符编码格式

3）如果时需要追加写入，可以使用 'a' 模式

`csv.writer()` 方法：参数：文件操作对象

```py
import csv

with open('data.csv', 'w'， encodeing='utf-8') as csvfile:
    # 参数一：文件操作对象
    writer = csv.writer(csvfile, delimiter=' ')
    # 先写标题后写值
    writer.writerow(['id', 'name', 'age'])
    writer.writerow(['1001', 'Mike', 20])
    writer.writerow(['1002', 'Juli', 28])
    writer.writerow(['1003', 'Bob', 40])
    
    # 多行同时写入
    writer.writerows([['1004', 'Gogo', 66], ['1005', 'Gimi', 65]])
    
    # 以字典的方式写入
    # 定义标题
    fieldnames = ['id', 'name', 'age']
    # 将标题初始化传给 DictWriter 方法以初始化一个字典写入对象
    writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
    writer.writerows([{'id': '1006', 'name': 'Anger', 'age': 22}, {'id': '1007', 'name': 'Lili', 'age': 36}])
```

==结果==

```csv
		id name age
		
		1001 Mike 20
		
		1002 Juli 28
		
		1003 Bob 40
		
		1004 Gogo 66
		
		1005 Gimi 65
		
		1006,Anger,22
		
		1007,Lili,36
```

#### 3、读取

通过构造 reader 对象，遍历文件中的内容；若csv文件中包含中文，还需要指定字符编码

```py
import csv

with open('data.csv', 'r', encoding='utf-8') as csvfile:
    # 构造 reader 对象，通过遍历输出文件中的内容
    reader = csv.reader(csvfile)
    for row in reader:
        print(row)
```



### 4.4 Mysql 存储

#### 1、准备工作

  `pip3  install pymysql`  

 

#### 2、数据库初始化

1.   连接数据库时，可以使用 db 来指定要调用的数据库
2.   cursor 方法获取数据库光标（命令执行权限）
3.   excute 方法执行数据库命令（但不能做增删改）

```py
import pymysql

db = pymysql.connect(host='127.0.0.1', user='root', password='Aa123456', port=3306, db='spiders')
# cursor 方法获取数据库的操作光标（命令执行权限）
cursor = db.cursor()
# 创建SQL语句
sql = "create table student(id varchar(10) primary key not null,name varchar(20) not null,age int not null)"
# 执行 SQL 语句
cursor.execute(sql)
# 关闭数据库
db.close()
```

#### 3、数据的增删改

1.   对数数据的 增删改 操作都需要额外调用 db对象的 commit 方法来实现，这个方法才是真正将语句提交到数据库执行的方法
2.   如果执行失败，则调用 rollback 执行数据回滚，相当于什么都没有发生过
3.   在执行 insert 语句时，若需要插入字符串，则需要额外 **添加引号** 包裹
4.   数据的 更新和删除 则再此基础上修改 sql 语句即可

```py
import pymysql

db = pymysql.connect(host='127.0.0.1', user='root', password='Aa123456', port=3306, db='spiders')
# cursor 方法获取数据库的操作光标（命令执行权限）
cursor = db.cursor()

# 数据
id = '20230001'
user = 'Bob'
age = 20

try:
    # 创建SQL语句
    sql = "insert into student values('{}', '{}', {})".format(id, user, age)
    # 执行 SQL 语句
    cursor.execute(sql)
    # 在做 增删改 操作的时候，除了要调用 excute 方法，还需要调用 commmit 方法
    db.commit()

except Exception as e:
    print€
        # 这里使用 rollback() 做事务处理，即若插入失败则执行数据回滚操作
    db.rollback()

# 关闭数据库
db.close()
```

#### 4、数据的查询

1.   调用 cursor 的 rowcount 属性获取查询结果的条数
2.   fetchone 方法永远获取第一条数据
3.   fetchall 方法获取剩下的所有数据，以二元数据的方式返回
4.   由于 fetchall 会一下子将所有的数据全部抓取，当数据量非常庞大时，会大量消耗服务器资源，此时我们可以使用 fetchone + while 的方法来循环抓取所有数据（P143）

 

>   注意：python 的MySQL处理与 PHP 一样，也有指针的概念；如果数据一共有9条，使用 fetone 抓取一条，**指针下移**，后续再使用 fetchall 方法获取所有数据，只能获取到8条

```py
import pymysql

db = pymysql.connect(host='127.0.0.1', user='root', password='Aa123456', db='spiders')
cursor = db.cursor()

sql = 'select * from student'
try:
    cursor.execute(sql)
    # 返回查询到的数据条目数
    print('Count：', cursor.rowcount)

    # 抓取第一条数据
    one = cursor.fetchone()
    print('One：', one)

    # 抓取剩下的所有数据
    all = cursor.fetchall()
    print('all：', all)

    # 由于 fetchall 方法得到的时一个二元数组，所以使用循环遍历数据
    for i in all:
        print(i)
except Exception as e:
    print(e)
    db.rollback()

db.close()
```

#### 5、MySQL事务的4个属性

+   原子性（Atomicity）：事务开始后所有操作，要么全部做完，要么全部不做，不可能停滞在中间环节。事务执行过程中出错，会回滚到事务开始前的状态，所有的操作就像没有发生一样。

+   一致性（Consistency）：事务执行的结果必须是使数据库从一个一致性状态变到另一个一致性状态。当数据库只包含成功事务提交的结果时，就说数据库处于一致性状态。如果数据库系统运行中发生故障，有些事务尚未完成就被迫中断，这些未完成的事务对数据库所作的修改有一部分已写入物理数据库，这时数据库就处于一种不正确的状态，或者说是不一致的状态。比如A向B转账，不可能A扣了钱，B却没收到。（也可以理解为系统从一个正确的状态，迁移到另一个正确的状态）。

+   隔离性（Isolation）：同一时间，只允许一个事务请求同一数据，不同的事务之间彼此没有任何干扰。比如A正在从一张银行卡中取钱，在A取钱的过程结束前，B不能向这张卡转账。

+   持久性（Durability）：指一个事务一旦提交，它对数据库中数据的改变就应该是永久性的。接下来的其他操作或故障不应该对其执行结果有任何影响。

## 第五章：Ajax 数据爬取

### 5.1 什么是Ajax

>   在浏览器中可以看到正常显示的页面数据，而使用request得到的结果中并没有这些数据。这是因为 request 获得的都是原始HTML文档，而浏览器中的页面是JavaScript处理数据后生成的结果

 

什么是Ajax？

​        Ajax是一门技术，利用Javascript在保证页面不被刷新，URL不被修改的情况下与服务器交互数据以更新网页内容



```javascript
<script>
    function loadDoc() {
        // 实例化并创建 XMLHttpRequest 对象
        var xhttp = new XMLHttpRequest();
        // 当响应得到处理时就会执行 onreadystatechange，一共会被触发5次，但只有状态值=4并且状态码=200时才会触发改变
        xhttp.onreadystatechange = function (ev) {
            if (this.readyState === 4 && this.status === 200){
                // 获取对象并使用inner.HTML改变网页内容
                document.getElementById("demo").innerHTML = this.responseText;
            }
        };
        // 以GET方法发送请求，请求文件是当前目录下的 first.txt，使用异步请求
        xhttp.open("GET", "first.txt", true);
        // 发送请求
        xhttp.send();
    }
</script>
```

### 5.2 Ajax 分析方法

使用 Google 浏览器 开发者工具来分析Ajax文件

1.   选择 XHR 类型：筛选出所有 Ajax 文件
2.   一般 Ajax 报文都会在u Request 中有一个信息：X-Requested-With:XMLHttpRequest，以此标记了请求是Ajax请求

![image-20230509184241191](Python%20%E7%BD%91%E7%BB%9C%E7%BC%96%E7%A8%8B%E5%BC%80%E5%8F%91%E5%AE%9E%E6%88%98.assets/image-20230509184241191.png)

### 5.3 Ajax 数据爬取实战

>   实战爬取内容：电影名、作者、电影简介

```py
# 爬取目标：标题（name）、剧情简介（drama）
import re
import requests
import urllib3
import logging
import pymysql

# 日志系统搭建
# 创建记录器
logger = logging.getLogger("scrape.center")
# 记录器等级为 INFO
logger.setLevel(logging.INFO)
# 创建处理器
consoleHandler = logging.StreamHandler()
# 处理器等级为 INFO
consoleHandler.setLevel(logging.INFO)
# 设置输出格式
fomatter = logging.Formatter('%(asctime)s|%(levelname)-8s|%(filename)s|%(lineno)s|%(message)s')
# 组装 logger
consoleHandler.setFormatter(fomatter)
logger.addHandler(consoleHandler)

# 数据库连接
db = pymysql.connect(host='127.0.0.1', user='root', password='Aa123456', port=3306, db='ajas')
cursor = db.cursor()

# 由于证书不受信任所以需要屏蔽证书访问警告
urllib3.disable_warnings()

for page in range(1, 21):
    try:
        url = "https://spa1.scrape.center/api/movie/" + str(page)
        # 日志输出
        logger.info('正在爬取网址：{}'.format(url))
        # 不对证书进行验证
        request = requests.get(url, verify=False)

        # 正则匹配 电影名
        moveie_name = re.compile('"id":' + str(page) + ',"name":"(.*?)",').findall(request.text)
        logger.info('电影名：{}'.format(moveie_name[0]))

        # 正则匹配 作者
        alias = re.compile('"alias":"(.*?)",').findall(request.text)
        logger.info("作者名：{}".format(alias[0]))

        # 正则匹配 剧情简介
        drama = re.compile('"drama":"(.*?)",').findall(request.text)
        logger.info("剧情简介：{}".format(drama[0]))

        # 数据库写入
        sql = "insert into A_info values({},'{}','{}','{}')".format('null', moveie_name[0], alias[0], drama[0])
        # print(sql)
        cursor.execute(sql)
        db.commit()
    except Exception as e:
        logger.error("爬取信息出现错误：{}".format(e))

```

