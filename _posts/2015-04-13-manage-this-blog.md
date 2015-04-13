---
layout: post
title: "Manage this blog"
description: "How to manage this blog?"
category: Manual
tags: [Jekyll, Github]
section: archive
---
{% include JB/setup %}

This post will describe the steps to manage the blog on Gihub.

# Start a new blog 

See the previous post: [URL]()

# Manage this blog

##1 . Add/Del posts.

Using the `rake` command to create new posts, and `git rm` to delete old posts.   

```
# Add post   
rake post title="name"

# Del post
git rm _post/2015-04-**_123
```

##2 . Add/Del pages.

Using the `rake` command to create new pages.

```
rake page name="newPage"
```

It will create a new folder of your input name, that contain a `index.html` file.

##3 . Change the style of this blog.

Suppose you are familiar with the directory structure of `Jekyll` static blogs. A basic Jekyll site usually looks something like this:     
Find more details at [Jekyllrb.com](http://jekyllrb.com/docs/structure)

```
.
├── _config.yml
├── _drafts
|   ├── begin-with-the-crazy-ideas.textile
|   └── on-simplicity-in-technology.markdown
├── _includes
|   ├── footer.html
|   └── header.html
├── _layouts
|   ├── default.html
|   └── post.html
├── _posts
|   ├── 2007-10-29-why-every-programmer-should-play-nethack.textile
|   └── 2009-04-26-barcamp-boston-4-roundup.textile
├── _data
|   └── members.yml
├── _site
├── assets
|   ├── images *images in this site.
|   └── themes *css files
├── pages
└── index.md
```

### _config.yml   
Stores `configuration` data. Caution about the `url` title and name.

### _drafts   
The posts you do not want to publish can be placed in this folder. `title.MARKUP`.   

### _includes   
Most of the `theme` and `JB` files are in this folder.   
`/_includes/themes/mark-reid/` :    
    `settings.yml`,    
    `default.html` : the default part of page/post in `html` format.   
    `page.html` : the default format of `pages`   
    `post.html` : the default format of `posts`   
`/_includes/JB/`   
   `posts_collate` : return the variable ` include JB/posts_collate `   
   `tags_list` : return the variable ` include JB/tags_list %}`   
   `categories_list` : return the variable `include JB/categories_list`   
   `comments` :  return the variable include custom/comments 
    ...
    
### _layouts   
These are the templates that wrap posts. The liquid tag `content`  is used to inject content into the web page.   
These files will be overwritted according to the changes in _include/theme/...

### _posts   
Your dynamic content. the files must follow the format: YEAR-MONTH-DAY-title.MARKUP

### _site   
This is where the generated site will be placed once Jekyll is done transfroming it. It's probably a good idea to add this to `.gitignore` file.

### index.html (or index.md, Textile files)   
Provided that the file has a YAML Front Matter section, it will be transformed by Jekyll. (also for .html, .markdown, .md or .textile).

### assets    
Change the main CSS file `/assets/themes/mark-reid/css/screen.css` for the layout of the site.

Frequently para: #header, #footer, pre, code ...

margin/padding: up right bottom left.

- font
  - `font-size : 14px;`, with `line-height: 20px`;
  - `font-family : "Galdeano","Hiragino Sans GB","Microsoft YaHei",Trebuchet,"Trebuchet MS",Tahoma,"Lucida Grande","Lucida Sans Unicode",Verdana,sans-serif;`
  - 
  - disqus & Google analytics

### pages    
The collection of all the pages in this blog. eg: `/pages/about.md`    

--------------------------------------------------
   
# Examples:    

## 1. Change the layout of `header` in `CSS` file.   

![](/assets/images/blog-header.png)

File: `/assets/themes/mark-reid/css/screen.css`

The html code of the header:   

```html
<div id="header">
    <h1>
    	<a href="/" title="M's Space">M's Space</a>
    </h1>
    <ul class="nav">
      <li><a class="home" href="/">Home</a></li>
      <li><a class="archive" href="/pages/archive.html">Archive</a></li>
      <li><a class="categories" href="/pages/categories.html">Categories</a></li>
      <li><a class="links" href="/pages/links.html">Links</a></li>
      <li><a class="about" href="/pages/about.html">About Me</a></li>
    </ul>
  </div>
```

The CSS code in file `screen.css`   

```
/* @group Header */
#header, #header a { color: #F2F2F2; border-bottom: none; }
#header:hover a { text-decoration: none; }
#header {
	position: relative;
	font-variant: small-caps;
    padding-left: 1em;
    border: 1px solid white;
    background-color: #585858;
    font-family: "Helvetica",Palatino,georgia,"times new roman",serif;
}
#header h1 a{
    font-size: 16px;
	font-weight: bold;
	line-height: 20px;
	font-family: "Helvetica",Palatino, georgia, "times new roman", serif;
}
#header ul {
	text-align: right;
	position: absolute;    
	display: inline;
	margin: 0;
    top: 0;
    right: 0;
	width: 80%;
}
#header ul li { display: inline; }
#header ul li a {
    font-size: 13px;
    line-height: 4em;
	padding: 0.5em;
	display: inline;
}
#header ul li a:hover {
	color: #1C1C1C;
    background-color: white;
    line-height: 100%;
	border-bottom: none;
    text-decoration: none;
}
/*
body #home #header a.home,
body #archive #header a.archive,
body #categories #header a.categories,
body #links #header a.links,
body #about #header a.about
 { 
    color: #585858;
    background-color: white;
}
*/
/* @end */
```

## 2. Jekyll variables

categories.path can be found in `config.yml`:    

```
JB :
  # These paths are to the main pages Jekyll-Bootstrap ships with.
  # Some JB helpers refer to these paths; change them here if needed.
  #
  archive_path: /pages/archive.html
  categories_path : /pages/categories.html
  tags_path : /pages/tags.html
  links_path : /pages/links.md
  about_path: /pages/about.md
```

Aware about the config, `site.JB.categories_path` will return '/pages/categories.html'.

*.md or *.html must be consistent with the files in `/pages`.

**Never mix is up**.

## 3. Preview the files in `_draft`    

you can run either of the following command to preview your draft as the latestt post.

```ruby
jekyll serve --draft 

jekyll build --draft 
```





