---
layout: post
title: 创建Github blogs
description: "About Github pages"
category: Github
tags: [Github, pages, blog]
---

> **Q:** Can we publish blogs using Github?  
> **A:** Yes. 

## 1. Why Github pages?  

  * Jekyll + Github can manage a Static webpage.  

  * Easy to manage your pages using `git` command.

  * Also support your own domain. 


## 2. What tools you need?    

  * `git` tools: [http://windows.github.com](http://windows.github.com)    
  * `jekyll` followning @[Julian Thilo's](http://jekyll-windows.juthilo.com/) instructions.

## 3. Start using pages

   1. Create pages for your `project`  
     * using `gh-pages` branch  
     * username.github.com/reponame

Goto the `settings` page of your repo, and you can find `Automatic page generator` icon. Done.

   2. Generate Github pages for your own.       
     * have to use the name of the account.  
     * Only one "URL" for one Github account. `name.github.com` 

Useful links  
Suppose you are familiar with the Github, can using `push` and `pull` commands. Also you can find more details [here 1](https://help.github.com/articles/set-up-git) & [here 2](http://www.thinkful.com/learn/a-guide-to-using-github-pages/) 


=================================

## Create User Pages

## 1. Create a repo `project`    
* Create a repo named "username" on github web site, and choose option: "initialize this repository with a README"    
* Clone the repo to your local PC   

``` 
    git clone git@github.com:username/project      
    cd username/  
    git checkout  --orphan  gh-pages
```    

We'll get a message saying **Switching to a new branch 'gh-pages'
* Make the gh-pages as the default branch

#### Make a Page
* Create a file called *index.html* in *project* folder    
```
    This is a demo for GitHub Pages.
```    
* Then commit and push our changes    
```
    git add --all .        
    git commit -m 'test page'        
    git push    
```    

#### Done, the first page
You can find the page at: [http://bakerwm.github.io/MingDemo](http://bakerwm.github.io/MingDemo)





