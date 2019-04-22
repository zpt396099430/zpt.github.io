---
title: 基于VPS的 SSR && V2ray 搭建脚本
date: 2019-04-16 17:32:03
tags: VPS
---
本文主要记录了自己搭建出国留学工具的过程中所用到的linux脚本，共分为三部分：
- BBR加速
- SSR
- V2ray
<!-- more -->

## BBR加速
### 脚本一键安装 BBR 加速
```shell
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh
```
### 验证Google BBR是否成功开启脚本
```shell
sysctl net.ipv4.tcp_available_congestion_control --bbr cubic reno 或者 reno cubic bbr
sysctl net.ipv4.tcp_congestion_control           --bbr
sysctl net.core.default_qdisc                    --fq
/*以上输出值返回正确说明 Google BBR 拥塞控制算法已经安装成功，注意：并不是所有的 VPS 都会有此返回值，若没有也属正常。*/
```

## SSR
### CentOS6/Debian6/Ubuntu14 ShadowsocksR一键部署管理脚本：
```shell
yum -y install wget
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/ssr.sh && chmod +x ssr.sh && bash ssr.sh
```
输入脚本后根据提示输入数字安装即可，快捷管理的命令为：``bash ssr.sh``

![](https://ws2.sinaimg.cn/large/747944d4ly1g25kxu0887j20c90aqq3f.jpg)

## V2Ray
[V2Ray一键安装脚本](https://github.com/233boy/v2ray/wiki/V2Ray%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85%E8%84%9A%E6%9C%AC)
