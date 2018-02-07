---
layout: post
title:  "Introducing Python About Web"
date:   2018-01-31
categories: Python
tags: Python Web JSON Scrap API
---

* content
{:toc}

There are three important concepts:  
* HTTP  
* HTML  
* URL

There are three layouts in Web:  
* Client:   
* Server:  
* Web API and Service

## Web Client

Web是一个客户端-服务器系统。客户端向服务器发起请求：它会创建一个TCP/IP连接， 通过HTTP发送URL和其他信息并接收一个响应。响应的格式也由HTTP定义。其中包括请求的状态以及（如果请求成功）响应的数据和格式。  
最著名的Web客户端是Web浏览器。它可以用很多种方式来发起HTTP请求。你可以在 地址栏中输入URL或者点击网页上的链接来手动发起请求。通常来说，请求所返回的数 据会被当作网页展示——HTML文档、JavaScript文件、CSS文件和图片——但也可以是 其他类型的数据，它们并不会被用于展示。

HTTP的一个重要概念是无状态。你发起的每个HTTP请求都和其他请求互相独立。可以使用cookie来解决无状态带来的问题。服务器可以在cookie中加入一些特殊的信息.

### telnet

    telnet www.google.com 80
    
如果google.com的80端口有一个Web服务器程序（我觉得这是肯定的），telnet会打印出一些确认信息并显示一个空白行供你输入信息：

    Trying 172.217.10.228...
    Connected to www.google.com.
    Escape character is '^]'.

### Python's Stardard Web Libraries

* http会处理客户端-服务器HTTP请求的具体细节：
  * client会处理客户端的部分client-side stuff  
  * server编写Python Web服务器程序  
  * cookies和cookiejar会处理cookie，cookie可以在请求中存储数据

* urllib是基于http的高级库：
  * request处理客户端请求client request  
  * response处理服务端的响应server request    
  * request处理客户端请求client request
  * parse会解析URL

使用标准库来获取网站的内容。例子中的URL会返回一段随机文本
``` python
import urllib.request as ur 
url = 'https://stemgene.github.io/2018/01/Markdown-Syntax/' 
conn = ur.urlopen(url) 
print(conn)
```

read()方法会获取网页的数据
``` python
data = conn.read()
print(data)
```

使用第三方模块request可以更短并且更易读

``` python
import requests
url = 'https://stemgene.github.io/2018/01/Markdown-Syntax/'
resp = requests.get(url)
resp
```

    print(resp.text)
    
## Web Servers

## Web Services and Automation

Web has turned out to be a powerful way to glue applications and data in many more formats than HTML

### The webbrowser Module

The webbrowser makes your browser do all the work

```python
import webbrowser
url = 'http://www.python.org'
webbrowser.open(url)
```
### Web APIs and Representational State Transfer表述性状态传递

Instead of publishing web pages, you can provide data through a web application programming interface(API). Clients access your service by making requests to URLs and getting back responses containing status and data. Instead of HTML pages, the data is in formats that are easier for programs to consume, such as JSON or XML.

### JSON

JavaScript Object Notation（JSON，http://www.json.org）是源于JavaScript的当今很流行的 数据交换格式，它是JavaScript语言的一个子集，也是Python合法可支持的语法。

用dumps将数据编码成JSON字符串，然后再反过来用loads把JSON字符串解析成python的数据结构： 首先给出一组数据menu

```python
import json
menu_json = json.dumps(menu)
menu_json
```

```python
menu2 = json.loads(menu_json)
menu2
```
### Crawl and Scrape

The information is available only in HTML pages. It's much more satisfying to automate some or all of these steps. An automated web fetcher is called a crawler or spider.

### Scrape HTML with BeautifulSoup

If you already have the HTML data from a website and just want to extract data from it, BeautifulSoup is a good choice.

    $ pip install beautifulsoup4
    
Let's use it to get all the links from a web page. The HTML *a* element represnts a link, HTML的a元素表示一个链接，and *herf* is its attribute representing the link destination.

```python
def get_links(url):
	import requests
	from bs4 import BeautifulSoup as soup
	result = requests.get(url)
	page = result.text
	doc = soup(page)
	links = [element.get('href') for element in doc.find_all('a')]
	return links

if __name__ == '__main__':
	import sys
	for url in sys.argv[1:]:
		print('Links in', url)
		for num, link in enumerate(get_links(url), start = 1):
			print(num , link)
		print()
```

I saved this program as *get_links.py* and then ran this command:
    
    $ python get_links.py http://stemgene.github.io


