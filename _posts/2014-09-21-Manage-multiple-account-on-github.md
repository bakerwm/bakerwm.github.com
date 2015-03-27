---
layout: post
title: 管理多个Github账号  
description: "manage multiple accounts on Github"
category: Github
tags: [Github, account, multiple]
---

> **Q:** How can we manage multiple [Github](github.com) IDs on one PC?
>
> **A:**　As far as I can see,　add a file `config` to the dir `~/.ssh/` can solve it. 

## 1 . Change your settings
1 . First of all, you have two Github IDs (ie: `A` & `B`).

2 . Create the SSH rsa keys for both of the IDs.      

```
## For A
ssh-keygen -t rsa -C "A_mail@gmail.com"
## save the key to file ~/.ssh/id_rsa_A

## For B
ssh-keygen -t rsa -C "B_mail@gmail.com"
## sage the key to file ~/.ssh/id_rsa_B
```
3 . Config your Github account settings

Log in Github use one of your ID (A), and go to `settings` -> `SSH keys` -> `Add SSH key`, then add the content of file ~/.ssh/id_rsa_A.pub to the blank.

The content like this:

```
ssh-rsa AAAABBBBCCCCDDDD... == A_mail@gmail.com

```

Change your ID (B) on Github and repeat above steps to add keys to Github.

4 . Create `config` in `~/.ssh/`

  * Create a empty file `config` in your dir `~/.ssh/`  
  * Add the following content to `config`  

```
# account A
Host github.com
	HostName github.com
	User git
	IdentityFile ~/.ssh/id_rsa_A

# account B
Host B.github.com  ## you can change `B` to your favorite word
	HostName github.com
	User git
	IdentityFile ~/.ssh/id_rsa_B

```

## 2 . Test your settings

```
$ssh -T git@github.com 
Hi **A**! You’ve successfully authenticated, but GitHub does not provide shell access.  
	
$ssh -T git@B.github.com  
Hi **B**! You’ve successfully authenticated, but GitHub does not provide shell access.  
```   

A and B will differ in your test depends on your names of the Github accounts.

## 3 . Examples

  1 . clone a git A to local using account A.

```
git clone git@github.com:A_name/A.git 
```

  2 . clone a git B to local using account B.

```
git clone git@B.github.com:B_name/B.git
```

## 4 . Last

Using `git@B.github.com` instead of `git@github.com` to manage your files on B account. 

