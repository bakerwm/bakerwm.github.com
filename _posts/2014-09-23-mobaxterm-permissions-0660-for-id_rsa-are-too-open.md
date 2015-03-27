---
layout: post
title: "MobaXterm: Permissions 0660 for 'id_rsa' are too open"
description: ""
category: MobaXterm 
tags: [MobaXterm, Windows]
---
# Environment:  
* MS Windows 8.1  
* Cygwin 64bit  
* ssh keys copied from somewhere and placed to ~/.ssh/

# Problem:  
```
ssh  -T git@github.com
```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
**Permissions 0660 for '~/.ssh/id_rsa' are too open.**  
It is required that your private key files are NOT accessible by others.  
This private key will be ignored.  
bad permissions: ignore key: ~/.ssh/id_rsa  

# Did not work:  
* chmod 600 id_rsa   
* chmod 700 .ssh   
* Playing with Windows security panels  
* Setting group to SYSTEM  
* Everything else  

# Solution:
Found on Vineet Gupta's blog http://vineetgupta.com/blog/cygwin-permissions-bug-on-windows-8

```
chgrp Users id_rsa
chmod 600 id_rsa
```

Now, it works!
