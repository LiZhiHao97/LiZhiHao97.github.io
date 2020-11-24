---
title: hexo的使用与部署
date: 2018-03-18 17:18:20
tags: 前端
categories:
 - 前端
 - hexo 
---



#### github page

在github 上的项目名称必须和自己用户名一样

自己的用户名是aaa项目名得是 aaa.github.io

安装hexo ,一些工具(git,node)  略过

# 关联 Hexo 与 GitHub Pages
我们如何让本地git项目与远程的github建立联系呢？用 SSH keys

## 生成SSH keys

输入你自己的邮箱地址
```shell
ssh-keygen -t rsa -C "940166841@qq.com"
```
在回车中会提示你输入一个密码，这个密码会在你提交项目时使用，如果为空的话提交项目时则不用输入，我们按回车不设置密码。
## 添加 SSH Key 到 GitHub

打开 C:\Users\lizhihao \ .ssh\id_rsa.pub，此文件里面内容为刚才生成的密钥，准确的复制这个文件的内容，粘贴到 https://github.com/settings/ssh 的 new SSH key 中

## 测试

可以输入下面的命令，看看设置是否成功，git@github.com的部分不要修改：    
```shell
ssh -T git@github.com   
```
如果是下面的反馈：
>  The authenticity of host 'github.com (207.97.227.239)' can't be established.
RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
Are you sure you want to continue connecting (yes/no)?

不要紧张，输入yes就好，然后会看到：

> Hi Lizhihao97! You've successfully authenticated, but GitHub does not provide shell access.

## 配置Git个人信息

现在你已经可以通过 SSH 链接到 GitHub 了，还有一些个人信息需要完善的。
Git 会根据用户的名字和邮箱来记录提交。GitHub 也是用这些信息来做权限的处理，输入下面的代码进行个人信息的设置，把名称和邮箱替换成你自己的。
```shell
git config --global user.name "lizhihao97"
git config --global user.email "940166841@qq.com"
```
## 配置 Deployment

在_config.yml文件中，找到Deployment，然后按照如下修改，用户名改成你的：

需要注意的是：冒号后面记得空一格！
```yml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: git@github.com:lizhihao97/lizhihao97.github.io.git
  branch: master
```
## 本地文件提交到 GitHub Pages
```shell
// 删除旧的 public 文件
hexo clean

// 生成新的 public 文件
hexo generate
或者
hexo g

// 开始部署
hexo deploye
或者
hexo d
在浏览器中输入 https://lizhihao97.github.io （用户名改成你的）看到了 Hexo 与 GitHub Pages 已经成功关联了
```

#### 注意1：
若上面操作失败，则需要提前安装一个扩展：
```shell
npm install hexo-deployer-git --save
```

## 写文章
写一篇文章,默认是以post作为模板
```shell
hexo new first(文章名)

```
tags里面写标签
```
tags:
- 前端
- 后端

分类
categories:
- 技术
```
##### 生成页面
```
 hexo new page about  //以page 作为模版,会自动生成里面的index.md
```

##### 搜索功能的实现
> https://github.com/alexbruno/hexo-generator-json-content



![](/image/hexo的一些使用和配置/hexo的一些使用和配置-1.png)

> 部分学习自：http://www.jianshu.com/p/39562a0d8eb6

# 出错
## 部署出错
Error: Host key verification failed.
fatal: Could not read from remote repository.
Please make sure you have the correct access rights and the repository exists.

解决方法：条件SSHkey
$ ssh-keygen -t rsa -C "imsofter@163.com"
三次回车，即可设置密码为空

将生成的C:\Users\用户名.ssh目录下的id_rsa.pub添加到github上


再测试是否可以连接到github上，
$ ssh git@github.com
Hi imsofter! You've successfully authenticated, but GitHub does not provide shell access.
Connection to github.com closed.



