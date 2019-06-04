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
![控制台.jpg](https://i.loli.net/2019/04/28/5cc50fa42f3c9.jpg)

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

![截图1.jpg](https://i.loli.net/2019/04/28/5cc51077c77f3.jpg)

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
![截图2.jpg](https://i.loli.net/2019/04/28/5cc510779ac88.jpg)
### 修改完成后，再下按 esc 键，然后再输入:wq
```shell
:wq
```
![截图3.jpg](https://i.loli.net/2019/04/28/5cc51077ac879.jpg)
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
# 修改默认SSH端口
## 修改/etc/ssh/sshd_config
使用`vi /etc/ssh/sshd_config`编辑配置文件
```
#Port 22         //这行去掉#号，防止配置不好以后不能远程登录，还得去机房修改，等修改以后的端口能使用以后在注释掉
Port 33378      //下面添加这一行
```
## 修改firewall配置(如果没有开启，则忽略)
firewall添加想要修改的ssh端口：
```
添加到防火墙：
firewall-cmd --zone=public --add-port=33378/tcp --permanent (permanent是保存配置，不然下次重启以后这次修改无效)
重启：
firewall-cmd --reload
查看添加端口是否成功，如果添加成功则会显示yes，否则no
firewall-cmd --zone=public --query-port=33378/tcp
```
## 修改SELinux
使用命令`semanage port -l | grep ssh`查看当前SElinux 允许的ssh端口
使用命令`semanage port -a -t ssh_port_t -p tcp 33378`添加33378端口到 SELinux
使用命令`semanage port -l | grep ssh`确认是否添加成功
如果成功会输出`ssh_port_t                tcp    33378, 22`
重启SSH服务`systemctl restart sshd.service`  
## 测试新端口的ssh连接
测试修改端口以后的ssh连接，如果成功则将step1里面的port 22 重新注释掉

本文转载自[Vedio Talk - Linux](https://www.vediotalk.com/?p=606)