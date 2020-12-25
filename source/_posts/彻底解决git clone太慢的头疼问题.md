---
title: 彻底解决git clone太慢的头疼问题
date: 2020-03-15 16:14:03
index_img: https://i.loli.net/2020/12/25/VzQe65t3dW8qabu.png
categories:
 - git
tags:
  - git
---

这段时间我电脑`git clone`的速度一直比较慢，在网上搜了许多比较坑的经验贴，都没有解决。

最近由于经常需要用到`git clone`，我就又静下心在网上开始找解决方案，最后终于解决了

我解决的方法如下：

首先查找域名对应的ip地址，并修改hosts文件

```shell
nslookup github.global.ssl.fastly.Net
nslookup github.com 
```

将下列内容加入 /etc/hosts文件中:

```shell
31.13.97.245 github.global-ssl.fastly.net
13.229.188.59 github.com
```

> 我不太懂计算机网络，但是我发现网络上许多类似教程中给的ip地址和我在终端获取到的是不一样的，这也是跟着许多教程走无效的原因，所以得自行获取

刷新DNS缓存

```shell
sudo /etc/init.d/networking restart // linux
ipconfig /flushdns                 // windows
sudo dscacheutil -flushcache       // macos
```

经过以上步骤，我的git clone速度从可怜的几kb到了十余MB

