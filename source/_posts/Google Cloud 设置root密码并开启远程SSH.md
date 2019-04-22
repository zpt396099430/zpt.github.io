---
title: Google Cloud 设置root密码并开启远程SSH
date: 2019-04-19 13:39:45
tags: 
- Linux
- VPS
categories: 教程
---


# 设置root密码
## 从Google Cloud控制台选择在浏览器窗口中打开ssh
![image](https://ws3.sinaimg.cn/large/747944d4ly1g27w8coanoj20qh086t95.jpg)

## 切换到root账号
```shell
sudo -i
```
<!-- more -->
## 设置root密码
```shell
passwd
```
然后会要求输入新密码，然后再重复一次密码，输入密码的时候不会显示出来，所以直接输入密码，然后回车，再然后重复输入密码回车

![image](https://wx4.sinaimg.cn/large/747944d4ly1g27wauu6lxj20hk04rt90.jpg)

# 开启SSH权限
## 方法一
### 修改SSH配置文件/etc/ssh/sshd_config
```shell
vi /etc/ssh/sshd_config
```
### 然后再输”i”进入编辑模式
```shell
i
```
### 找到以下内容并修改
```shell
PermitRootLogin yes           //默认为no，需要开启root用户访问改为yes
PasswordAuthentication yes    //默认为no，改为yes开启密码登陆
```
![image](https://wx2.sinaimg.cn/large/747944d4ly1g27wejubuhj2071049wee.jpg)
### 修改完成后，再下按 esc 键，然后再输入:wq
```shell
:wq
```
![image](https://wx3.sinaimg.cn/large/747944d4ly1g27wfgbj5tj207p03gmx2.jpg)
### 重启服务器
```shell
reboot
```

## 方法二
### CentOS和Debian通用，输入以下两条命令
```shell
sed -i 's/PermitRootLogin no/PermitRootLogin yes/g' /etc/ssh/sshd_config
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
```
### Ubuntu系统，输入以下两条命令
```shell
sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
```
### 重启服务器
```shell
reboot
```

本文转载自[Vedio Talk - Linux](https://www.vediotalk.com/?p=606)