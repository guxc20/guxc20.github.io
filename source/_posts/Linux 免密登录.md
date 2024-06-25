---
title: Linux 免密登录
date: 2023-12-09 13:32:49
tags: Linux
categories: Linux
---

## 步骤：
> 机器A：本地或远程的 Mac、Linux 机器
> 机器B：需要登录的Linux服务器
> 实现：从 机器A 免密码登录到 机器B

## 机器A上操作
1、生成公钥/私钥对
```
先进入到.ssh目录看一下是否已有公私钥，没有生成
cd ~/.ssh
ssh-keygen -t rsa
```
2、拷贝公钥到机器B的任意目录
```
scp id_rsa.pub root@10.10.14.30:/root/gxc-test
```
## 机器B上操作
写入authorized_keys
```
cat ～/gxc-test/id_rsa.pub >> ~/.ssh/authorized_keys
```
注意：authorized_keys 需要 600 权限
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys

参考[http://www.5ityx.com/cate100/319376.html](http://www.5ityx.com/cate100/319376.html)
