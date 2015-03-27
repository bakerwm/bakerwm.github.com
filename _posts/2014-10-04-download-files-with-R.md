---
layout: post
title: 通过R语言下载文档
description: ""
category: Manual
tags: [R, Download]
---
使用R批量下载文件

说明  
主要是在懒得挨个链接区另存为，所以折腾了一段R代码来完成

#1 思路  
 * 1 提取一个URL下所有链接地址  
 * 2 批量下载符合规则的文件  

#2 工具
 * 1 download.file()    
 另一个相同功能的下载函数 download()来自downloader包
 
```R
install.packages("downloader")
library(downloader)
```
  
  
#3 实践
下载二进制文件时，**需要使用 mode="wb" 参数**。
  
###1 下载单个PDF文件
  
```R
url <- "http://rfunction.com/code/1202/par-120208.pdf"
download.file(url, basename(url), mode="wb")

```

###2 下载单个MP3文件
这里以GRE词汇的录音文件为例：[Links](http://download.dogwood.com.cn/online/grechjx/)

```R
url <- "http://download.dogwood.com.cn/online/grechjx/WordList01.mp3"
download.file(url, basename(url), mode="wb" )

```

**此处若缺少 mode="wb" 参数，则下载的文档无法正常播放 **
  
  
###3 批量下载文本文件 "R code" 
文件列表详见[链接]("http://rfunction.com/code/1202/")  
此处批量获取下载链接的方法引用 COS论坛 上 谢益辉 的帖子：[链接](http://cos.name/cn/topic/101030)

```R
# 批量获取链接
library(XML)
listLinks = function (URL, pattern = "", relative = FALSE) {
    doc = htmlParse(URL)
    if (is.null(grep("/$", URL)))
        URL = dirname(URL)
    nodes = getNodeSet(doc, "//a[@href]")
    hrefs = sapply(nodes, function(x) xmlGetAttr(x, "href"))
    paste(if (relative)
        ""
    else URL, hrefs[grep(pattern, hrefs)], sep = "")
}

url <- "http://rfunction.com/code/1202/"
Rfiles <- listLinks(url, pattern="\\.R$")
destDir <- tempdir()
  
# 批量下载文档
for(i in Rfiles) 
	download.file(i, file.path(destDir, basename(i)))
	
```

顺利下载所有R文档

```R
trying URL 'http://rfunction.com/code/1202/120201.R'
Content type 'text/plain' length 1726 bytes
opened URL
downloaded 1726 bytes

...

```
  
最后，再引用 谢益辉 在[COS论坛](http://cos.name/cn/topic/101030)上介绍的方法，获取多级目录下的文件链接，并下载。

```R
library(XML)
downloadSite = function(URL, web.ext = c("html", "shtml"),
    pattern = "", verbose = getOption("verbose")) {
    web.rec = tempfile()
    writeLines(URL, web.rec)
    # top directory
    URL.top = if (length(grep("/$", URL)))
        URL
    else paste(dirname(URL), "/", sep = "")
    fullInfoErrorHandler = function(msg, code, domain, line,
        col, level, file) {
        # level tells how significant the error is
        #   These are 0, 1, 2, 3 for WARNING, ERROR, FATAL
        #     meaning simple warning, recoverable error and fatal/unrecoverable error.
        #  See XML:::xmlErrorLevel
        #
        # code is an error code, See the values in XML:::xmlParserErrors
        #  XML_HTML_UNKNOWN_TAG, XML_ERR_DOCUMENT_EMPTY
        #
        # domain tells what part of the library raised this error.
        #  See XML:::xmlErrorDomain
        codeMsg = switch(level, "warning", "recoverable error",
            "fatal error")
        if (verbose)
            message("(!)There was a ", codeMsg, " at line ",
                line, " column ", col, "\n(!)", msg, appendLF = FALSE)
    }
    listLinks = function(URL, web.ext = web.ext, pattern = pattern) {
        if (verbose)
            timestamp()
        URL.dir = if (length(grep("/$", URL)))
            URL
        else paste(dirname(URL), "/", sep = "")
        if (verbose)
            message("Parsing ", URL)
        if (inherits(try(con <- url(URL, open = "r")), "try-error")) {
            if (verbose)
                message("Found an invalid link: ", URL, "\n")
            return(NULL)
        }
        else {
            close(con)
            doc = htmlParse(URL, error = fullInfoErrorHandler)
            nodes = getNodeSet(doc, "//a[@href]")
            # all links
            hrefs = sapply(sapply(nodes, function(x) xmlGetAttr(x,
                "href")), URLdecode)
            if (verbose)
                message("Found ", length(hrefs), " links in total...")
            hrefs = gsub("^[[:space:]]+|[[:space:]]+$", "", hrefs,
                useBytes = TRUE)
            # links to top URL
            hrefs = sub("^/", URL.top, hrefs, useBytes = TRUE)
            # remove links to other domains
            hrefs = hrefs[!(substr(hrefs, 1, 7) == "http://" &
                substr(hrefs, 1, nchar(URL.top)) != URL.top)]
            if (verbose)
                message(length(hrefs), " link(s) within the domain...")
            # remove email links
            if (length(mail.idx <- grep("mailto.*@.*\\..*", hrefs,
                useBytes = TRUE)))
                hrefs = hrefs[-mail.idx]
            if (verbose)
                message(length(hrefs), " link(s) left when I removed email links...")
            # get relative paths
            hrefs = gsub(URL.dir, "", hrefs, fixed = TRUE, useBytes = TRUE)
            # match specified patterns
            hrefs = hrefs[grep(pattern, hrefs, useBytes = TRUE)]
            if (verbose)
                message(length(hrefs), " link(s) matched with the pattern \"",
                  pattern, "\"...")
            # get absolute paths
            abs.hrefs = if (length(hrefs) == 0)
                NULL
            else {
                gsub("\\.\\./", "/", paste(URL.dir, hrefs, sep = ""),
                  useBytes = TRUE)
            }
            # find out links to web pages (*.html/shtml)
            web.idx = grep(paste("\\.", web.ext, "$", sep = "",
                collapse = "|"), hrefs, useBytes = TRUE)
            if (verbose)
                message("Found ", length(web.idx), " further webpage links in the current page...")
            old.rec = readLines(web.rec)
            web.addr = setdiff(abs.hrefs[web.idx], c(old.rec,
                ""))
            cat(web.addr, file = web.rec, sep = "\n", append = TRUE)
            if (length(web.idx) == 0) {
                if (verbose)
                  message("No further webpage links can be found...\n")
                return(abs.hrefs)
            }
            else {
                if (verbose) {
                  message("I'll look for links in further pages...\n")
                  flush.console()
                }
                return(c(abs.hrefs, unlist(sapply(web.addr, listLinks,
                  web.ext = web.ext, pattern = pattern))))
            }
        }
    }
    all.links = unique(c(URL, listLinks(URL, web.ext, pattern)))
    all.links
}
# 下载部分待续

options(verbose = TRUE)

lnk = downloadSite("http://www.public.iastate.edu/~pdixon/stat500/")

lnk = downloadSite("http://www.public.iastate.edu/~dnett/S511/stat511.shtml")

# 不要拿太复杂的网站做测试，因为嵌套太深会让程序跑很长时间
```





