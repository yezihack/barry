---
title: "github-webhook工具实现github自动构建"
date: 2020-04-24T15:04:27+08:00
lastmod: 2020-04-24T15:04:27+08:00
draft: false
tags: ["Go语言", "tool", "github-webhook", "自动构建"]
categories: ["工具"]
author: "百里"
comment: true
toc: true
reward: true
# weight: 1
# lastmod: 2018-03-01T16:01:23+08:00
# reward: false
# mathjax: false
# description = ""
---

# 原理
当本地`git push origin master`向Github远程仓库提交代码时，可以通过配置github自带webhook向服务器发送请求，
利用github-webhook工具在服务器端接到请求后，调用自定义shell脚本来实现自动构建
![github-webhook](https://upload-images.jianshu.io/upload_images/13827699-49011566250e8250.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# github-webhook 文档
- 更多详情文档
[https://github.com/yezihack/github-webhook](https://github.com/yezihack/github-webhook)


# 使用github-webhook
> 源码: [https://github.com/yezihack/github-webhook](https://github.com/yezihack/github-webhook) 别忘记点个小星星哦.

## 1. 下载github-webhook工具
[https://github.com/yezihack/github-webhook/releases](https://github.com/yezihack/github-webhook/releases)

 github release下载太慢, 试试这个
```
wget http://img.sgfoot.com/github-webhook1.4.1.linux-amd64.tar.gz
```

## 2. 运行github-webhook
安装
```
tar -zxvf github-webhook1.4.1.linux-amd64.tar.gz
cp github-webhook /usr/bin/
chmod u+x /usr/bin/github-webhook
```
运行
- 默认端口: 2020
- 有效访问地址: http://ip:2020/web-hook 
- `-b` 是shell脚本路径参数
- `-s` 是github webhook设置的密码
```
# 非后台运行
github-webhook -b [shell脚本路径] -s [github webhook设置的密码]

# 后台运行
nohup github-webhook -b [shell脚本路径] -s [github webhook设置的密码] & 

# 定向日志输出
nohup github-webhook -b ~/sh/hugo2www.sh -s qweqwe >> ~/logs/webhook.log 2>&1 &
```

# 配置github webhook
- 填写你服务器的地址, http://ip:2020/web-hook
- 设置的密码必须与服务器运行`github-webhook -s `设置的密码一致.

![配置第一步](https://upload-images.jianshu.io/upload_images/13827699-4aa2488f63658de4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![配置第二步](https://upload-images.jianshu.io/upload_images/13827699-f3866693a5db8df2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![配置第三步](https://upload-images.jianshu.io/upload_images/13827699-09a4de85b8b2b006.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# 测试
git push后, 你就可以看到github推送的信息
```
git push origin master 
```
