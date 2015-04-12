---    
layout: post    
title: RCurl的问题    
description: ""    
category: R    
tags: [R, RCurl, htmlTreeParse]    
section: archive
comments: true
---    

最近学习 [炼数成金](http://www.dataguru.cn/) 的网络课程 《R七种武器之网络爬虫RCurl》，对HTML完全不懂，从头学起。    
首先举个例子，利用RCurl取得网页信息。    


```R
library(RCurl)    
library(XML)    
url <- "http://tuan.baidu.com/movie"    
doc <- htmlTreeParse(url, useInternal=TRUE)    
tgComp  <- getNodeSet(doc, "//a[@class='n-sitename']")    
tgTitle <- getNodeSet(doc, "//div[@class='good-n-meta clearfix']")    
tgPrice <- getNodeSet(doc, "//div[@class='good-n-price']")    
##提取相应数值    
Comp  <- iconv(xmlSApply(tgComp, xmlValue),  from="utf-8", to="")    
Title <- iconv(xmlSApply(tgTitle, xmlValue), from="utf-8", to="")    
Price <- iconv(xmlSApply(tgPrice, xmlValue), from="utf-8", to="")    
#Delete ￥, 折等    
Price <- gsub("[^0-9. ]", "", Price)    
#分别提取价格等    
temp <- sapply(Price, function(x) strsplit(x, split="\\s+", perl=TRUE))    
Table <- do.call(rbind.data.frame, temp)    
#制作data.frame    
colnames(Table) <- c("Blank", "Price", "Pre-Price", "Discount")    
rownames(Table) <- 1:nrow(Table)    
Table$Title <- Title    
Table$Company <- Comp    
Table$Blank <- NULL    
Table <- Table[c(5, 4, 1, 2, 3)]    
Table <- sapply(Table, function(x) gsub("^\\s+|\\s+$", "", x))    
colnames(Table) <- c("团购网", "团购名称", "团购价", "原价", "折扣")    
head(Table)    
```

团购信息前几条如下：    

```R
团购网    
[1,] "百度糯米"    
[2,] "百度糯米"    
[3,] "百度糯米"    
[4,] "百度糯米"    
[5,] "百度糯米"    
[6,] "百度糯米"    
团购名称    
[1,] "[兜有电子商务]单人3D影票！奢华进口设备，震撼视听享受，刺激你的眼球！"    
[2,] "[博纳国际影城]顺义店单人影票（含3D）！全场通兑！豪华影音设备，国内外大片随心看！节假日通用！"    
[3,] "[耀莱成龙国际影城]慈云寺店双人影票（含3D）！盛大开业，劲爆超低价疯狂来袭，不补差价"    
[4,] "[通州区电影院]单人影票（含3D）！大片连连上映，精彩不容错过，期待您的到来，节假日通用！"    
[5,] "[魔幻岛5D影院]4人观影套票！无需预约，多人套票更超值！惊险刺激的现场画面，如亲临现场！"    
[6,] "[网票网]3D电影票1张！精彩大片轮番演绎，震撼银幕效果，超值享受！"    
团购价 原价  折扣    
[1,] "61.5" "120" "5.1"    
[2,] "35"   "100" "3.5"    
[3,] "49"   "160" "3.1"    
[4,] "26.5" "80"  "3.3"    
[5,] "56"   "120" "4.7"    
[6,] "62"   "120" "5.2"    
```
总结几个问题：    
====================    

1. 保存html页面后用XML处理，使用参数： useInternal=TRUE    

2. 部分中文网页遇到乱码是，可使用 iconv()进行处理，    

(原理未知。 )    

```R
#中文乱码情况    
xmlValue(tgComp[[1]])    
[1] "  鐧惧害绯背  "    
#转换后即可正常显示中文    
iconv(xmlValue(tgComp[[1]]), from="utf-8", to="")    
[1] "  百度糯米  "    
```

对htmlTreeParse()进行指定encoding操作也无法获取正确的中文编码，如：GBK，GB2312  
    
3. sapply()函数    

在此处的应用可对变量连续进行某一公式计算。    

```R
p <- iconv(xmlSApply(tgPrice, xmlValue), from="utf-8", to="")    
p[1]    
[1] "    ￥61.5   ￥120  5.1折  "    
p1 <- sapply(p, function(x) strsplit(x, split="\\s+", perl=TRUE))    
p1[[1]]    
[1] ""       "￥61.5" "￥120"  "5.1折"    
```

将"character"分割后，得到的变量类型为"list"。  
    
4. R数据类型转换：list -> data.frame    

此处使用rbind函数进行操作。    

```
p2 <- do.call(rbind.data.frame, p1)    
head(p2)    
```

转换后的结果如下    

```
	                           c.............................................................    
￥61.5   ￥120  5.1折    
￥35   ￥100  3.5折    
￥49   ￥160  3.1折    
￥26.5   ￥80  3.3折    
￥56   ￥120  4.7折    
￥62   ￥120  5.2折    
                        c...61.5.....35.....49.....26.5.....56.....62.....88...    
￥61.5   ￥120  5.1折                                                    ￥61.5    
￥35   ￥100  3.5折                                                        ￥35    
￥49   ￥160  3.1折                                                        ￥49    
￥26.5   ￥80  3.3折                                                     ￥26.5    
￥56   ￥120  4.7折                                                        ￥56    
￥62   ￥120  5.2折                                                        ￥62    
                        c...120.....100.....160.....80.....120.....120.....150...    
￥61.5   ￥120  5.1折                                                       ￥120    
￥35   ￥100  3.5折                                                         ￥100    
￥49   ￥160  3.1折                                                         ￥160    
￥26.5   ￥80  3.3折                                                         ￥80    
￥56   ￥120  4.7折                                                         ￥120    
￥62   ￥120  5.2折                                                         ￥120    
                        c..5.1折....3.5折....3.1折....3.3折....4.7折....5.2折....5.9折...    
￥61.5   ￥120  5.1折                                                               5.1折    
￥35   ￥100  3.5折                                                                 3.5折    
￥49   ￥160  3.1折                                                                 3.1折    
￥26.5   ￥80  3.3折                                                                3.3折    
￥56   ￥120  4.7折                                                                 4.7折    
￥62   ￥120  5.2折                                                                 5.2折    
```
