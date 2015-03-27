---
layout: post
title: "编辑Linux的$PATH"
categories:
-  Linux
tags:
- PATH
- bashrc
- bash_profile

comments: true
---
linux环境变量设置错误后，如何恢复解决方案：在命令行中输入：

```
export PATH=/usr/bin:/usr/sbin:/bin:/sbin:/usr/X11R6/bin 
```
后回车

`/etc/profile`: 此文件为系统的每个用户设置环境信息,当用户第一次登录时,该文件被执行.并从/etc/profile.d目录的配置文件中搜集shell的设置.   

`/etc/bashrc`:为每一个运行bash shell的用户执行此文件.当bash shell被打开时,该文件被读取.~

`/.bash_profile`:每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件 仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件.

`~/.bashrc`:该文件包含专用于你的bash shell的bash信息,当登录时以及每次打开新的shell时,该该文件被读取.

`~/.bash_logout`:当每次退出系统(退出bash shell)时,执行该文件. 

`/etc/profile`中设定的变量(全局)的可以作用于任何用户,而~/.bashrc等中设定的变量(局部)只能继承 /etc/profile中的变量,他们是"父子"关系.~/.bash_profile 是交互式、login 方式进入 bash 运行的~/.bashrc 是交互式 non-login 方式进入 bash 运行的通常二者设置大致相同，所以通常前者会调用后者。 www.linux010.cn (来源：linux010) 