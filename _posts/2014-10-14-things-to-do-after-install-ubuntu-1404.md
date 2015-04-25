---
layout: post
title: 安装Ubuntu 14.04 之后需要做的几件事
description: ""
category: ubuntu
tags: [ubuntu, install]
---
使用中文界面的Ubuntu，需要做一些调整，更方便使用。主要包括系统优化、软件安装。

## 1. 更改软件更新源：  
在系统设置 -> Ubuntu 软件 -> 下载自，调整镜像为 http:// mirrors.163.com    

## 2. 安装软件：    
* 1) 新立得件管理包：在`Ubuntu软件中心`搜索 新立得 即可。    
* 2) Chromium: 在`Ubuntu软件中心`搜索并安装 chromium    
* 3) 搜狗拼音for Linux：前往http://pinyin.sogou.com/linux/ 下载搜狗输入法for Linux, Ubuntu 14.04可直接双击deb包安装。    
* 4) WPS office for Linux: 前往http://community.wps.cn/download/下载最新版 deb 包，双击安装即可    
* 5) VLC媒体播放器

## 3. 系统美化    

* 1 更改中文字体    
安装文泉驿黑字体   

```
sudo apt-get install ttf-wqy-microhei
```

* 2 删除原默认字体 ukai, uming    

```
sudo apt-get remove fonts-arphic-ukai fonts-arphic-uming 
```

* 3 主题美化工具    
在`Ubuntu软件中心`安装美化工具 `Unity Tweak Tool`    
或者前往 http://ubuntu-twewak.com/ 下载 `Ubuntu Tweak`的deb包，双击安装即可。    

## 4. Adobe Flash Player
* 1 安装Adobe Flash Player (pepper flash player)

```
sudo apt-get update
sudo apt-get install chromium-browser
sudo apt-get install pepperflashplugin-nonfree
sudo update-pepperflashplugin-nonfree --install
```

* 2 软件中心搜索adobe flash player安装即可

## 6. 安装Jekyll & git 管理github上的blog    
Jekyll 2 was recently released and can be installed on Ubuntu 14.04 using `apt-get` and `gem install`. Additional features such as Rdiscount can be added with the same meth od. Although there are packages for Jekyll in the Ubuntu repository, the packages are for older versions of Jekyll.
See the reference:[Install Jekyll 2 on Ubuntu 14.04](http://michaelchelen.net/81fa/install-jekyll-2-ubuntu-14-04/)    
### Step 1 Insall Prerequisites   
Install ruby, the ruby development libraries, and the make command.

```
sudo apt-get install ruby ruby-dev make
```

### Step 2 Install Jekyll    
Install the Jekyll gem system wide. To include all documentation, omit the `--no-rdoc  --no-ri` switches.

```
sudo gem install jekyll --no-rdoc  --no-ri
```

### Step 3 ExecJS Workaround    
Install **nodejs** to work around this issue

```
sudo apt-get install nodejs
```

### Step 4 Start Jekyll    
Check that Jekyll has been successfully installed.    

```
jekyll -v
```

### Step 5 Recommended    
Install other required packages.  

``` 
# Install git
sudo apt-get install git

# Install Rdiscount Markdown renderer
sudo gem install rdiscount  --no-rdoc --no-ri

# Install Redcarpet Markdown renderer
sudo gem install redcarpet  --no-rdoc --no-ri

# Install 汉字to拼音 
sudo gem install hz2py --no-rdoc --no-ri
```

### Check you site locally    
Start the website website HTML and start a local server.

```
jekyll serve
```

# 7. 安装Bioinfo-tools
* 1 R 3.1.1 for ubuntu 14.04    

```
sudo add-apt-repository ppa:marutter/rrutter
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install r-base r-base-dev
```

* 2 Rstudio for ubuntu 14.04    
前往 http://www.rstudio.com/products/rstudio/download/ 下载相应版本的 deb 包，双击安装。

* 3 Bioconductor    

```
source("http://bioconductor.org/biocLite.R")
biocLite()
```

* 4 Biopython
a. Install Biopython dependencies
Numerical Python (NumPy)
sudo apt-get install python-numpy python-scipy

```
# Check 
>>> import numpy
```

b. Install ReportLab
The ReportLab package is a library for generating PDF documents.

```
sudo pip install rlextra 
-i https://wangm08@yeah.net:gongpeng!110@www.reportlab.com/pypi/
# For an automated install, the command line must split over separate lines.

# Check
>>> from reportlab.graphics import renderPDF
```

c. Install Biopython
Download biopython distribution: http://biopython.org/wiki/Download.
Unpack the file with "tar -xzvpf biopython-**.tar.gz"
Then move into the biopython* directory, and then install biopython with "sudo python setup.py install"

d. Check everything is worked
Option 1:

```python
>>> from Bio.Seq import Seq
>>> from Bio.Alphabet.IUPAC import unambiguous_dna
>>> seq = Seq('GATCAGAAG', unambiguous_dna)
>>> seq[0:2]
>>> seq.translate()
```

Option 2:

```
python setup.py test
```

* 5 Install third-party tools from Synaptic    
a. NCBI Standalone BLAST    
b. clustalo, libargtable,    
c. EMBOSS,     

* 6 Install samtools
* 7 install bedtools  

  `sudo apt-get install bedtools`

* 8 


============================
1. Unable to access Windows partitions

Error messages:

```
Unable to access “OS”
Error mounting /dev/sda1 at /media/ming/OS: Command-line `mount -t "ntfs" -o "uhelper=udisks2,nodev,nosuid,uid=1000,gid=1000" "/dev/sda1" "/media/ming/OS"' exited with non-zero exit status 14: Windows is hibernated, refused to mount.
Failed to mount '/dev/sda1': Operation not permitted
The NTFS partition is in an unsafe state. Please resume and shutdown
Windows fully (no hibernation or fast restarting), or mount the volume
read-only with the 'ro' mount option.
```

**Solution**

Run the following commands in your terminal:

```
sudo ntfsfix /dev/sd**NN**/
```

the **NN** is the partition you want to mount, (in my examples, it is sda1)

[Warnnings: I do not know whether this solution will crash your windows.]


# 8. 修改启动顺序  

在Windows 8.1 系统上安装的 Ubuntu 15.04, 安装后默认的首选启动是： "Ubuntu"

```
×Ubuntu  
  Advanced options for Ubuntu 
  Memory test (memtest86+)  
  Memory test (memtest86+, serial console 11520)
  Windows 8 (loader) (on /dev/sda1)  
```

Ubuntu 15.04 using GRUB2 to manage the boot order.

Two ways to modify the boot order:

- 1 . recommended:

```
$ sudo mv /etc/grub.d/30_os-prober /etc/grub.d/08-os-prober
```

The above command is changing the name of `30_os-prober` to `08_os-prober` (06-09 is ok). The filename relate to Ubuntu boot are begin with "10_", so "08_" is prior to the Ubuntu. At last, we have to update the grub by `sudo update-grub`

The output:  

```
$ sudo update-grub
[sudo] password for ming: 
Generating grub configuration file ...
Found Windows 8 (loader) on /dev/sda1
Found linux image: /boot/vmlinuz-3.19.0-10-generic
Found initrd image: /boot/initrd.img-3.19.0-10-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

Done, it turns out that `Windows 8` is on the top.


- 2 . alternative way

Open the file `/etc/default/grub` and change the value of `GRUB_DEFAULT=` from `0` to `4` ( the 5th is windows, ubuntu begin with 0".

```
$ sudo vim /etc/default/grub
```

```
# If you change this file, run 'update-grub' afterwards to update
# /boot/grub/grub.cfg.
# For full documentation of the options in this file, see:
#   info -f grub -n 'Simple configuration'
 
GRUB_DEFAULT=0  # change it to 4
#GRUB_HIDDEN_TIMEOUT=0
GRUB_HIDDEN_TIMEOUT_QUIET=true
GRUB_TIMEOUT=10
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX=""

...

```

Need to update the grub.

```
$ sudo update-grub
```

Done!

Reboot and you will find the default boot option is `Windows 8`.



