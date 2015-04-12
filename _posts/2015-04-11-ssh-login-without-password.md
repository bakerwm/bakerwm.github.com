---
layout: post
title: "SSh login without password [2 steps]"
description: ""
category: Linux
tags: [SSH, rsa]
---
{% include JB/setup %}

For instance, I want access to my CentOS server from my PC (Ubuntu) without input the password. 

**[What]** SSH from Ubuntu to CentOS without passwd.    

**[How]** Let the CentOS know the public rsa key of my Ubuntu.     

**Step 1.** On Ubuntu: Create public/private **RSA** key pair.    

```
ssh-keygen -t rsa 
```

Default, it will create two files: `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub`.

**Step 2.** Copy `id_rsa.pub` of Ubuntu to `~/.ssh/authorized_keys` on CentOS

```
scp ~/.ssh/id_rsa.pub  yourname@192.168.1.30:/home/wangming/.ssh/temp.pub   
## 192.168.1.30: ip of CentOS
## You need to input the passwd this time

## log in CentOS
ssh yourname@192.168.1.30   # Input passwd of CentOS again    
cat ~/.ssh/temp.pub >> ~/.ssh/authorized_keys   # copy Ubuntu public rsa key to CentOS authorized  keys    
chmod 600 ~/.ssh/authorized_keys    

```

Finish. I can SSH to CentOS from my Ubuntu without passwd now.

```
ssh yourname@192.168.1.30    # no need passwd
```

Just in the same way, I can generate and copy the public rsa key of CentOS to `~/.ssh/authorized_keys` on Ubuntu to let CentOS log in Ubuntu without passwd.    
