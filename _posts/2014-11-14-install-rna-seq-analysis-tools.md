---
layout: post
title: "RNA-Seq分析工具的配置"
categories:
- RNA-Seq
- Linux
tags:
- samtools
- bedtools
- bioconductor
- edgeR
- DEseq
- R
- python
- perl
- bowtie
- soap

comments: true
---

当前有很多RNA-Seq分析工具，本文主要记录在Linux + Windows下常用的软件安装注意事项。

update: 2014-11-14
OS: 
Linux系统下一般使用 --prefix将程序安装到自定义的路径。再通过修改 ~/.bash_profile 将该路径添加到 $PATH方便使用。



1. python 2.7.8  
从www.python.org[这里](https://www.python.org/downloads/release/python-278/)下载系统对应的python版本。  
Linux X86_64 版本: 
Python 2.7.8 source release [Gzipped source tarball](https://www.python.org/ftp/python/2.7.8/Python-2.7.8.tgz)   
MD5sum: d4bca0159acb0b44a781292b5231936f  
size: 14846119  

安装在个人目录：$HOME/localpython/

```
tar zxvf Python-2.7.8.tgz
cd Python-2.7.8
./configure --prefix=$HOME/localpython/
make 
make install
```

* 安装Python modules

查看当前Python的安装目录：

```{python}
$ python
Python 2.7.8 (default, Nov 14 2014, 13:47:10)
[GCC 4.4.7 20120313 (Red Hat 4.4.7-3)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import sys
>>> sys.prefix
'/home/wangming/localpython'
>>> sys.exec_prefix
'/home/wangming/localpython'

```

如果使用系统默认安装的python (2.6.6)：

```{python}
$ /usr/bin/python
Python 2.6.6 (r266:84292, Oct 12 2012, 14:23:48)
[GCC 4.4.6 20120305 (Red Hat 4.4.6-4)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import sys
>>> sys.prefix
'/usr'
>>> sys.exec_prefix
'/usr'
```
若对以上目录无写入权限，则需要安装python modules至有写入权限的路径：

```
python setup.py  install --prefix=$HOME/localpython/
```
















2. CentOS: $PATH 备份        

Linux系统下$PATH配置：    

```
/usr/lib64/qt-3.3/bin
/usr/kerberos/bin
/usr/kerberos/sbin
/usr/local/bin
/usr/local/sbin
/bin
/sbin
/usr/bin
/usr/sbin

$HOME/bin
$HOME/.cpan
$HOME/bin
$HOME/bin/lib
$HOME/software/R-3.1.1/bin
$HOME/software/Python-2.7.8/bin
$HOME/software/blast-2.2.24/bin
$HOME/software/soap2.20release
$HOME/software/tophat-2.0.13.Linux_x86_64
$HOME/software/samtools-1.1
$HOME/software/bowtie-1.1.1
$HOME/software/bowtie2-2.2.4
$HOME/software/bedtools2-2.21.0/bin
$HOME/software/stacks-1.21/bin
$HOME/software/fastx_toolkit-0.0.14/bin
$HOME/software/samstat-1.5/bin
$HOME/.local/bin
```







