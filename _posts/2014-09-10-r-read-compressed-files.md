---
layout: post
title: R语言读取压缩文档
description: "Read *.gz, *.bz2 files in R"
category: R
tags: [R, gz, bz2, Read]
section: archive
comments: true
---
使用R语言进行数据分析时，经常需要读取文档，经常会遇到压缩文档（.bz2, .gz)。

# Goal: Special cases in reading files

*  Reading a *.bz2 file  
```
read.table(bzfile("file.text.bz2"))           
```

* Reading a .gz file  
```
read.table(gzfile("file.text.gz"))      
```

*  Reading from a pipe   
```
mydata <- read.table(pipe("awk -f filter.awk input.txt"))  # under Linux system
```

* Reading from a URL    
```
read.table(url("http://www.mayin.org/ajayshah/A/demo.text"))
```

* Reading from a URL directly  
```
read.table("http://www.mayin.org/ajayshah/A/demo.text")
```

* Reading a bz2 file from URL  
```
read.table(pipe("links -source http://www.mayin.org/ajayshah/A/demo.text.bz2 | bunzip2"))
```

* Reading binary files from a URL  
```
load(url("http://www.mayin.org/ajayshah/A/nifty_weekly_returns.rda"))
```

source： <http://www.mayin.org/ajayshah/KB/R/html/r8.html>






