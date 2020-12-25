---
title: Node.js项目线上服务器部署与发布
date: 2019-01-12 20:37:13
index_img: https://i.loli.net/2020/12/25/OjXpFCrcAeSLJ8a.png
categories:
 - 运维
tags:
  - linux 
  - node 
  - 前端
  - 运维
---

## ssh
> 配置公钥 和密钥 可以登录服务器不需要密码
看是否已经存在公钥和密钥 
看用户文件夹下是否存在
![](/image/nodejs项目服务器部署/nodejs项目服务器部署-1.png)

![](/image/nodejs项目服务器部署/nodejs项目服务器部署-2.png)


ctrl +d 退出用户
sudo  rm -f  xxx 删除一个文件

```shell
//连接
ssh ubuntu@ip地址
```
```shell
// 先给root一个密码
sudo passwd root
// 然后切换到root su root
```

```shell
//在root下
sudo adduser lizhihao
增加一个用户

//然后在root下给skl sudo 权限
 gpasswd -a lizhihao sudo
 
 //
 sudo visudo
 增加下面的lizhihao
```
![](/image/nodejs项目服务器部署/nodejs项目服务器部署-3.png)

ctrl +X保存 之后shift+Y enter 退出

可以在root 下进入skl 用户 su skl 
或者 sudo su lizhihao 

-----------------------------------------------------------------
```shell
//重启服务
 sudo service ssh restart
```
```shell
//生成公钥和私钥
 ssh-keygen -t rsa -C "lizhihao@qq.com"
```

```shell
//配置文件 修改默认端口
sudo vi /etc/ssh/sshd_config
```
## 改过端口后 连接服务器会连不上 
出现    
     : ssh: connect to host ip地址 port 22: Connection refused

这时候就要虚入输入端口号了

-  ssh -p 8888 ubuntu@ip地址
(8888是刚刚改过的端口)

```shell
//更新
sudo apt-get update

//安装一些
sudo apt-get install vim openssl build-essential libssl-dev wget curl git

//找到github上的nvm 
https://github.com/creationix/nvm
找到安装脚本
```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash

![](/image/nodejs项目服务器部署/nodejs项目服务器部署-4.png)

装完后若发现nvm 不是一个命令 在来一个命令窗口

### 安装好nvm 就可以安装nodejs了
 > nvm install v8.2.1
 ### 指定一下node版本 
 > nvm use v8.2.2
 ### 设置一下默认
 > nvm alias default v8.2.1
### 设置成淘宝 
用一个nrm 包
> npm --registry=http://registry.npm.taobao.org install -g nrm 

> nrm use taobao 

### 安装一些包
>  npm i pm2 webpack gulp grunt-cli -g
###  查看全局安装过的包 npm list -g -depth 0


#### 创建 app.js  如下
> 创建 sudo  vi app.js 
```javascript
var http=require('http');
var server=http.createServer(function (req,res) {
    res.writeHead(200,{'Content-Type':'text/plain'})
    res.end('Hello SKL\n');
})
server.listen(8081);
console.log('Server running at http://ip地址:8081');

```


之后 node app.js
就可以在浏览器中访问 127.0.0.1:8081


### 使用nginx 80端口

> 停止apache 服务
   sudo service apache2 stop
   ```shell
 移除Apache 2
 
 sudo update-rc.d -f apache2 remove
 
 sudo apt-get remove apache2
 ```
 ```shell
 下载 nginx 
    sudo apt-get install nginx
```
## 配置
1. cd /etc/nginx/
2. cd conf.d
3. sudo vi lizhihao-cn-8081.conf  
写入
```
upstream blog{
    server 127.0.0.1:8081;
}

# NGINX Server Instance,PORT 80
server {
    listen 80;
    server_name ip地址;


    # Proxy to the Node instance
    location / {
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header Host $http_host;
      proxy_set_header X-NginX-Proxy true;
      proxy_pass http://blog;
      proxy_redirect off;
    }
 }

```
4.sudo nginx -t  测试是否成功
5.  sudo nginx -s reload 重启 nginx 

之后输入ip地址 就可以看到了 把8081 的服务都导向到Nginx的80端口

> 让 Nginx的版本信息在浏览器的头信息中不那么明显

```
cd /etc/nginx
sudo vi nginx.conf
去掉下面配置文件前的# 保存
``` 
去掉 
![](/image/nodejs项目服务器部署/nodejs项目服务器部署-5.png)
然后 sudo service nginx reload


### MongoDB ubuntu16.04 
https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/

```
命令：
xxxx

sudo apt-get install -y mongodb-org
其中这一步速度很慢，改成阿里云的连接
cd /etc/apt/sources.list.d
vi mongodb-org-3.4.list  (注意看安装时的版本)
改成：
deb [ arch=amd64,arm64 ] http://mirrors.aliyun.com/mongodb/apt/ubuntu xenial/mongodb-org/3.6 multiverse
命令：
// 改过镜像后要upadte一下
xxxx
xxx
xxx
```
```
 sudo service mongod start
 之后 mongo就启动了
```
```
若遇到无法写入。加sudo 

mongodb 默认跑在27017 端口上
```
```
//修改
sudo vi /etc/mongod.conf
port 改成了19997 
```

### 文件上传
```
 scp ./test.html lizhihao@ip地址0:/home/lizhihao/test
 scp -P 8888 ant-mobile-recruit-app.tar lizhihao@ip地址:/home/lizhihao/
```

### 打包
> tar
```
    -c ： 打包

    -v ： 显示过程

    -f ： 指定打包后的文件名
    
    -x : 解打包
    
    -z：压缩为.tar.gz格式
    
```

```
//压缩
tar -cvf japan.tar japan
tar -zcvf japan.tar.gz japan
//解压
tar -xvf japan.tar
tar -zxvf japan.tar

```

### 配置可以让域名访问服务器
```
cd /etc/nginx/conf.d
sudo mv lizhihao-cn-8081.conf www-lizhihao-cn-3000.conf
修改配置文件
upstream blog{                                                      
        server 127.0.0.1:3000;                                      
}                                                                   
# NGINX Server Instance,PORT 80                                     
server {                                                            
        listen 80;                                                  
        server_name www.lizhihao.cn;                            
# Proxy to the Node instance                                        
location / {                                                        
       proxy_set_header X-Real-IP $remote_addr;                     
       proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; 
       proxy_set_header Host $http_host;                            
       proxy_set_header X-NginX-Proxy true;                         
       proxy_pass http://blog;                                      
       proxy_redirect off;                                          
     }                                                              
}    
// 重启nginx
sudo nginx -s reload

```


### 配一下防火墙
```
sudo apt-get update && sudo apt-get upgrade
sudo iptables -F //先清掉所有的规则
sudo vi /etc/iptables.up.rules
------------------------------------------  
*filter
# 允许所有建立起来的连接
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
# 允许所有出去的流量
-A OUTPUT -j ACCEPT
-A INPUT -p tcp --dport 443 -j ACCEPT
-A INPUT -p tcp --dport 80 -j ACCEPT
# ping
-A INPUT -p icmp -m  icmp --icmp-type  8 -j ACCEPT
# allow ssh port login
-A INPUT -p  tcp -m state --state NEW --dport 8888 -j ACCEPT

# MongoDB connection
-A INPUT -s 127.0.0.1 -p tcp --destination-port 27017 -m state --state NEW,ESTABLISHED -j ACCEPT
-A OUTPUT -d 127.0.0.1 -p tcp --source-port 27017 -m state --state ESTABLISHED -j ACCEPT

# 3000端口
-A INPUT -s 127.0.0.1 -p tcp --destination-port 3000 -m state --state NEW,ESTABLISHED -j ACCEPT
-A OUTPUT -d 127.0.0.1 -p tcp --source-port 3000 -m state --state ESTABLISHED -j ACCEPT

# 9093端口
-A INPUT -s 127.0.0.1 -p tcp --destination-port 9093 -m state --state NEW,ESTABLISHED -j ACCEPT
-A OUTPUT -d 127.0.0.1 -p tcp --source-port 9093 -m state --state ESTABLISHED -j ACCEPT

# 记录被拒绝的请求
-A INPUT -m limit --limit 5/min -j LOG --log-prefix "iptables denied:" --log-level 7
# drop incoming sensitive connections
-A INPUT -p tcp --dport 80 -i eth0 -m state --state NEW -m recent --set
-A INPUT -p tcp --dport 80 -i eth0 -m state --state NEW -m recent --update --seconds 60 --hitcount 150 -j DROP
# reject all other inbound
-A INPUT -j REJECT
-A FORWARD -j REJECT

COMMIT  
-------------------------------------------
sudo iptables-restore </etc/iptables.up.rules //载入规则
sudo ufw status //查看防火墙状态
sudo ufw enable //激活

sudo vi /etc/network/if-up.d/iptables //让他开机自动启动
·-----------------------------------
#!/bin/sh
iptables-restore /etc/iptables.up.rulea
-----------------------------------------------------
sudo chmod +x /etc/network/if-up.d/iptables



### 出现invalid host header的问题

```
```
// react的话在config里面的webpackDevServer.config.js里面加一个
disableHostCheck: true

```
# https配置
```
// 在腾讯云上申请证书(具体详见文档：https://cloud.tencent.com/document/product/400/4143)
// 把证书下载下来上传到服务器
// 修改配置文件
upstream blog{
     server 127.0.0.1:3000;
}
# NGINX Server Instance,PORT 80
server {
        listen 80;
        server_name www.lizhihao.cn;
        # rewrite ^(.*) https://$host$1 permanent;
        return 301 https://www.lizhihao.cn$request_uri;
}
server {
        listen 443;
        server_name www.lizhihao.cn; #填写绑定证书的域名
        ssl on;
        ssl_certificate /home/lizhihao/https-key/1_www.lizhihao.cn_bundle.crt;
        ssl_certificate_key  /home/lizhihao/https-key/2_www.lizhihao.cn.key;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE; #按照这个套件配置
        ssl_prefer_server_ciphers on;

        # if($ssl_protocol = ""){
         #  rewrite ^(.*) https://$host$1 permanent;
        # }
# Proxy to the Node instance
location / {
       proxy_set_header X-Real-IP $remote_addr;
       proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
       proxy_set_header Host $http_host;
       proxy_set_header X-NginX-Proxy true;
       proxy_pass http://blog;
       proxy_redirect off;
        }
}
测试nginx 
重启nginx
```
## node 后端启动
```
nohup npm start &
https://segmentfault.com/q/1010000004455598
lsof -i 查看端口
```