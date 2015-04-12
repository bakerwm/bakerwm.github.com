---
layout: post
title: 在Github上管理个人blog
description: "Help me to manage the blog on github. step-by-step."
category: Github
tags: [github, blog, tutorial]
section: archive
comments: true
---

This tutorial will teach you to create a blog on github step-by-step.  
Mainly, 3 parts will included.  

**1 Create your blog using Jekyll-bootstrap. (see [Site](http://jekyllbootstrap.com/usage/jekyll-quick-start.html))**  
**2 Change the theme. (see [Site]())**  
**3 Write your post. (see [Site]())**  

Most of the following content come from [Jekyll-bootstrap quick-start](http://jekyllbootstrap.com/usage/jekyll-quick-start.html)  

Here will show you step-by-step:  

**1. Create your blog using Jekyll-bootstrap**

*Create a new repo on github: _bakerwm_.github.com (your username)  
*Install Jekyll-Bootstrap  
Enter these commands into your git terminal  

	git clone https://github.com/plusjade/jekyll-bootstrap.git USERNAME.github.com
	cd USERNAME.github.com
	git remote set-url origin git@github.com:USERNAME/USERNAME.github.com.git
	git push origin master


*Profit  
After a couple mintues your blog will be publicly available at http://bakerwm.github.com

*Check your blog locally
Go to your local directory, and run the following commands  


	jekyll serve


You need to install [Jekyll](http://jekyll-windows.juthilo.com/) on windows first.  

**2. Change the theme**

Install a theme by the url  


	rake theme:install git="https://github.com/jekllbootstrap/theme-the-program.git"


Or download the zip file to ./_theme_packages,


	rake theme:install name="Theme-name


Switch themes


	rake theme:switch name name="the-program"  


**3. Create a Post**

Using rake to create post


	rake  post  title="Hello-World"


The rake task will automatically creates a file with properly formatted filename and YAML Front Matter in _post directory.

Create a Page:


	rake page name="about.md"


Create a nested page:


	rake page name="pages/about.md"


Create a page with a "pretty" path:


	rake page name="pages/about"
	*# this will create a file: ./pages/about/index.html*


Publish your blog:


	git  add  .
	git  commit  -m  "Add new content"
	git  push  origin  master


This will automatically deploy your changes to your hosted blog.





