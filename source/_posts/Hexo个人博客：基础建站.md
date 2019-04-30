---
title: Hexo个人博客：基础建站
date: 2019-04-12 17:36:15
tags:
- 搭建博客
categories: 教程
---
## 前言
其实很久以前就有想法想要搭一个属于自己的博客，但是由于种种不可抗拒之力~~其实就是懒~~，最终才拖到现在才成功搭建起了自己的博客网站。
<!-- more -->
一开始选择了在vultr上购买了vps并安装了wordpress，但是wordpress实在是太过臃肿，相比而言hexo更加回归写作本身，所以最终我还是选择了hexo。一方面是由于hexo更纯粹，另一方面也是由于hexo的搭建需要借助github，借此正可以熟悉一下git的应用。
博客不在美，而在于勤。
望与诸君共勉。
## 遇到的问题
1. 域名访问以后出现403错误
2. Hexo中使用外链形式添加的图片无法被加载  

## 准备工作
1. Github账号一枚
2. VPS服务器
3. Git
4. Node.js
5. Xshell6  
6. 域名（需要实名备案）  

## 搭建过程

### Git和Node.js的安装
[Git](https://www.git-scm.com/download/)  
安装完成后执行`git version`命令查看版本信息，若显示则安装成功   
安装完成后执行一些初始化操作，打开Git Bash设置用户名和邮箱  
```
git config --global user.name "用户名"
git config --global user.email "邮箱地址"
```
 设置SSH Key，Git Bash内执行：`ssh-keygen -t rsa -C "邮箱地址"`根据提示输入密码等完成设置。  
 查看公开密钥：`cat ~/.ssh/id_rsa.pub`复制显示的内容，待会有用。  
[Node.js](https://nodejs.org/en/)  
安装完成后执行`node version`命令查看版本信息，若显示则安装成功

### 本地Hexo博客搭建

#### 使用npm安装hexo
```
npm install -g hexo-cli
```
 安装完成后在本地新建目录hexo(或任意名字自己决定)，例如D:\Blog，在此根目录下执行
```
hexo init
npm install
```
 执行完成后，可在根目录下执行`hexo clean && hexo g && hexo s -p 4000`  
 然在浏览器url中输入 http://localhost:4000 就可以看到你的个人博客了（端口如被占用可换成其他端口，如5000）。  
至此，本地Hexo博客搭建完成。

### Git Hooks自动部署

#### 部署原理
>通过在本地编辑文本，然后使用Git远程部署到VPS的Git仓库。hexo d命令实际上只deploy了本地的public文件夹，Git Hooks实际上就是当Git仓库收到最新的push时，将Git仓库接受到的内容复制到VPS上的网站目录内。相当于完成了手动将public文件夹复制到VPS的网站根目录里。

#### VPS操作
- 安装Git  
首先需要同本地一样安装Git，过程略。
- 创建Git用户  
执行：`adduser git`，根据提示设置密码。





