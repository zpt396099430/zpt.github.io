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
- 赋予git用户sudo权限  
执行：
```
chmod 740 /etc/sudoers
vim /etc/sudoers
```
 找到以下内容：
```
# User privilege specification
root    ALL=(ALL:ALL) ALL
```
 在`root ALL=(ALL:ALL) ALL`这一行下面添加`git ALL=(ALL:ALL) ALL`  
 `wq:`保存退出后，修改回文件权限`chmod 440 /etc/sudoers`  
- 初始化git仓库  
 切换到git用户`su git`然后：  
 ```
 cd /home/git                //切换到git用户目录
mkdir blog.git              //创建git仓库文件夹，以blog.git为例
cd blog.git             //进入仓库目录
git init --bare             //使用--bare参数初始化为裸仓库，这样创建的仓库不包含工作区
```
- 创建网站目录
```
cd /var/www/                //切换目录
mkdir blog              //创建网站目录，以blog为例
```
- 配置SSH
```
cd /home/git                //切换到git用户目录
mkdir .ssh              //创建.ssh目录
cd .ssh
vim authorized_keys
```
 然后将本地的公钥复制到authorized_keys文件里(公钥即上文中本地执行cat ~/.ssh/id_rsa.pub查看的内容)
- 用户组管理
执行：
```
ll /home/git/
ll /var/www/
```
 确保blog.git、.ssh、blog目录的用户组权限为git:git  
![](https://i.loli.net/2019/05/15/5cdbfaa45905728938.png)  
若不是，执行下列命令后再查看：
```
sudo chown git:git -R /var/www/blog
sudo chown git:git -R /home/git/blog.git
```

#### 安装配置nginx
- 安装nginx  
执行：`apt-get install nginx`，若输入`nginx -V`可以看到nginx版本信息，则安装成功。
- 配置nginx  
执行：
```
cd /etc/nginx/sites-available               //切换目录
cp default default.bak                  //备份默认配置
vim default                     //修改配置
```
 参靠我的本人配置文件内容：
```
server {
    listen 80 default;              //默认监听80端口
    root /var/www/blog;             //网站根目录
    server_name leihungjyu.com www.leihungjyu.com;  //网址
    access_log  /var/log/nginx/blog_access.log;
    error_log   /var/log/nginx/blog_error.log;
    error_page 404 =  /404.html;

    location ~* ^.+\.(ico|gif|jpg|jpeg|png)$ {
        root /var/www/blog;
        access_log   off;
        expires      1d;
    }

    location ~* ^.+\.(css|js|txt|xml|swf|wav)$ {
        root /var/www/blog;
        access_log   off;
        expires      10m;
    }

    location / {
        root /var/www/blog;
        if (-f $request_filename) {
        rewrite ^/(.*)$  /$1 break;
        }
    }

    location /nginx_status {
        stub_status on;
        access_log off;
    }
}
```
 保存退出后，启动`nginx：systemctl start nginx`  
 设置开机自动启动：`systemctl enable nginx`  
 查看运行状态：`systemctl status nginx`,显示`running`表示成功运行。  
 ![](https://i.loli.net/2019/05/15/5cdbfeef7949750207.png)  
 
#### 配置Git Hooks
 - 创建post-receive文件  
 git用户下执行：
 ```
 cd /home/git/blog.git/hooks     //切换到hooks目录下
 vim post-receive            //创建文件
 ```
   复制下面的内容到post-receive文件中：
 ```
 #!/bin/bash
 GIT_REPO=/home/git/blog.git
 TMP_GIT_CLONE=/tmp/blog
 PUBLIC_WWW=/var/www/blog
 rm -rf ${TMP_GIT_CLONE}
 git clone $GIT_REPO $TMP_GIT_CLONE
 rm -rf ${PUBLIC_WWW}/*
 cp -rf ${TMP_GIT_CLONE}/* ${PUBLIC_WWW}
 ```
   保存退出后，执行：chmod +x post-receive赋予可执行权限。  
   
#### 本地操作
 - 尝试连接  
 在本地打开Git Bash：`ssh git@VPS的ip`
 ![](https://i.loli.net/2019/05/15/5cdbfeef972e226206.png)  
 如图所示则连接成功，若默认端口不是22，则需要在后面加上-p 端口号，或者在.ssh文件夹下创建一个config文件，内容如下：
 ```
 Host 服务器IP
HostName 服务器IP
User git
Port 端口号
IdentityFile ~/.ssh/id_rsa
```
- 配置Hexo  
打开本地博客根目录下的_config.yml文件，找到最后的deploy配置，修改为：
```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
    type: git
    repo: git@VPS的IP:blog.git
    branch: master
```
 到此，Hexo建站就全部配置部署完毕了。



