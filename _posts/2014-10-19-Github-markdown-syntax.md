---
layout: post
title: "Github Markdown常用语法"
categories: 
- Github
tags:
- Github
- Syntax
- markdown

comments: true
---

日常使用Markdown文档时所需的语法：

## 1. 链接    
* 文字->URL链接    
使用 ` [Baidu](http://www.baidu.com) ` 的方式添加超级链接。

[Baidu](http://www.baidu.com)

* 图片    
使用：`![Text]({{ base.url }}/assets/images/Aboutme.jpg)` 格式    
![Local pic]({{ base.url }}/assets/images/Aboutme.jpg =600x)



## 2. 格式    
常规文字格式

* Headings    
在文字开头加入一个或多个 `#`，可控制文字大小

```
# Heading 1
## Heading 2
### Heading 3
#### Heading 4
```
显示效果如下：
# Heading 1
## Heading 2
### Heading 3
#### Heading 4

* Styling text    
对文字格式化，语法如下：

```
* italic *
** bold **
__bold__
~~delete~~
```
显示效果如下：

*italic*  
**bold**  
__bold__  
~~delete~~  

* Nested lists    
在list前加入两个空格可以做退格显示

```
1. Item 1
  1. A corollary to the above item.
  2. Yet another point to consider.
2. Item 2
  * A corollary that does not need to be ordered.
    * This is indented four spaces, because it's two spaces further than the item above.
    * You might want to consider making a new list.

```
显示效果如下：    
1. Item 1  
  1. A corollary to the above item.  
  2. Yet another point to consider.  
2. Item 2  
  * A corollary that does not need to be ordered.  
    * This is indented four spaces, because it's two spaces further   than the item above.  
    * You might want to consider making a new list.  

* Blockquotes
加入 `>` 即可显示为引用

```
In the words of Abraham Lincoln:
> Pardon my french
```
显示效果如下：

In the words of Abraham Lincoln:
> Pardon my french

## 3. Code formatting    
涉及两种情况需要做代码语法标记：  

* 个别单词   
在需要标记的文字前后加上一个  ` 即可。

```
Here's an idea: why don't we take `SuperiorProject` and turn it into `**Reasonable** Project`.
```
显示效果如下：    
Here's an idea: why don't we take `SuperiorProject` and turn it into `**Reasonable** Project`.

* 整段代码    
使用三个```标记，分离出一个断落，最好在开头处空出一行。

    Check out this neat program I wrote:

    ```
    x = 0
    x = 2 + 2
    what is x
    ```
    
    ```

* 语法高亮

使用如下方式可以进行语法高亮标记

    Check out this neat program I wrote:

Code:    

    ```python
    x = 0
    x = 2 + 2
    what is x
    ```   
Output:
    
```python
x = 0
x = 2 + 2
what is x
```

## 4. Task lists   

Code:
 
```
- [x] @mentions, #refs, [links](), **formatting**, and <del>tags</del> supported
- [x] list syntax required (any unordered or ordered list supported)
- [x] this is a complete item
- [ ] this is an incomplete item
```

显示效果如下：    
  - [x] @mentions, #refs, [links](), **formatting**, and <del>tags</del> supported  
  - [x] list syntax required (any unordered or ordered list   supported)  
  - [x] this is a complete item  
  - [ ] this is an incomplete item  











