---
layout: post
title:  "作死把root的登录shell环境改错路径的经历"
date:   2016-01-08 22:44:15
categories: 折腾
---

> 这是一个悲伤的故事，关键问题：root的登录shell环境改出问题无法登录了怎么办？


云梯在家里用实在太慢了，后来就买了conoha的vps，各种搭ss和vpn，最近用上了fishshell，折腾vps的手就顺手改了一下。

一直作死用的root账号，没想到`chsh`这个命令竟然没有防误操作回滚的机制，用`chsh`把root的登录shell环境改成fish之后，提示了几句话我没认真看，倒是去`/etc/passwd`里边看了一下，改动生效了，然后重启，然后就挂了...

然后症状就是一直没法ssh，连web console的登录都有问题，一登录就退出，整个人都崩溃了。一直到很后边才发现，原来我_**`把fish的路径搞错了`**_！！然后什么`su`啊都没法用了，简直是作死。

搜了一大把资料发现，如果是实体机，可以进入`单用户`或者安全模式把shell路径改掉，但是我这是vps，必须远程登录，就算在`vnc`里边，`reboot`的命令也需要`root`权限，所以单用户这条路走不通。

然后就是用另外一个拥有`sudo`权限的用户登录后去修改，结果发现我没创建`sudo group`的用户，卧槽要跪...

一段漫长的搜索之后几乎绝望了，感觉就剩等客服回应和还原两条路了。

然后突然发现了**`pkexec`**这个命令

    pkexec allows an authorized user to execute PROGRAM as another user. 
    If username is not specified, then the program will be executed as the
    administrative super user, root.
    
    pkexec允许一个合法的用户以另外一个用户的权限执行程序。
    如果没有明确指定用户名，那就会以管理员也就是root用户来执行程序。
    
感觉就是专门为这种场景而生的有木有！绕开了root的登录shell，直接

    pkexec bash

    
然后输入root的密码，搞定，赶紧修改把root的登录shell环境改回来。。


<br>

总结一下root登录shell环境出错导致root无法登录的解决方案
----

1. 如果有其他`sudoer`，登录后修改`/etc/passwd`
2. 如果没有`sudoer`，但是有其他用户，使用`pkexec`
3. 如果完全没有其他用户，那只能是去实体机器上登录，进入安全模式修改`etc/passwd`