---
layout: post
title: 通过Python批量下载文件
description: 
category: Tools
tags: [Python, download file]
---
使用Python时，通常会遇到下载文件的需要，直接下载是最快捷的方式。以下分别介绍两种工具下载文档的方法。

### Python
Python最常用的是使用urllib或者urllib2模块通过HTTP下载文档。  
以下介绍下载R文档的方法：

```python
import urllib
import urllib2
import requests

# Test URL
url = "http://rfunction.com/code/1202/120202.R"

# 1. Using urllib module
print "Begin download with urllib"
urllib.urlretrieve(url, "code1.R")

# 2. Using urllib2 module
print "Begin download with urllib2"
f = urlllib2.urlopen(url)
with open("code2.R", "wb") as code :
    code.write(f.read())
	
# 3. Using requests
print "Begin download with requests"
r = requests.get(url)
with open("code3.R", "wb") as code :
    code.write(r.content)

```
The **requests** library method is **get**, which corresponds to the **HTTP GET**. Then you just take the requests object and call its **content** property to get the data you want to write. We use the **with** statement because it will automatically close a file and simplifies the code. Note that just using **read()** for large file can be dangerous. It would be better to read it in pieces by passing **read** a size.

Posted by [Mike Driscoll](http://www.blog.pythonlibrary.org/). You can find the original post [Here](http://www.blog.pythonlibrary.org/2012/06/07/python-101-how-to-download-a-file/)

