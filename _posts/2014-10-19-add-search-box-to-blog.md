---
layout: post
title: "在Jekyll blog中添加搜索框"
categories: 
- Jekyll
tags:
- Jekyll
- search box

comments: true
---

使用Jekyll + Github管理blog后，希望添加本地搜索功能，折腾太久依旧没有搞定。这里先记录下当前做过的尝试：

1. [Google custom search](http://stackoverflow.com/questions/10131541/how-can-i-add-a-site-search-feature-to-a-jekyll-blog)  

2. [Jekyll + lunr](https://github.com/bakerwm/jekyll-lunr-js-search)
查阅这份repo中的相关描述。

3. [Tipue](http://www.tipue.com/search/)
官方文档中有详细说明。

4. [Crawler](http://blog.import.io/tech-blog/5-steps-to-creating-a-search-box-for-static-blogs)  
查阅相关blog的说明[5 Steps to Creating a Search Box for Static Blogs](http://blog.import.io/tech-blog/5-steps-to-creating-a-search-box-for-static-blogs)

苦于仅仅菜鸟一枚，上述方法依旧没有搞定。

### 我对Search box的设想是：

* 搜索窗口固定于页面顶部

* 输入关键词的同时即以下拉方式显示搜索结果

* 搜索范围包括：标签，分类，标题，全文



