---
title: Hyper-V快速安装的Ubuntu有时候登录(xrdp)连接没任何反应
img_path: /assets/img/posts/20231013/
categories: [错误笔记]
tags: [Hyper-V, Ubuntu, xrdp]
---

### Hyper-V 通过快速安装官方提高的 Ubuntu 有时候登录(默认使用 xrdp)连接显示纯色背景没任何反应

#### 原因

因为 xrdp 远程连接时之前你登录的 ubuntu 账号未注销导致

#### 解决方案

点击菜单栏`基本会话`图标按钮，进入 ubuntu 系统后注销之前登录的账号，再次点击`增强会话`图标按钮重新登录(xrdp)就行了

![addremote](hypervubuntuloginerror.webp)
