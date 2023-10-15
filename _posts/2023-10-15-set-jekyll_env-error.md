---
title: 无法将“JEKYLL_ENV=production”项识别为 cmdlet、函数、脚本文件或可运行程序的名称。请检查名称的拼写，如果包括路径，请确保路径正确， 然后再试一次
categories: [错误笔记]
tags: [jekyll-theme-chirpy, JEKYLL_ENV=production]
---

```bash
JEKYLL_ENV=production : 无法将“JEKYLL_ENV=production”项识别为 cmdlet、函数、脚本文件或可运行程序的名称。请检查名称的拼写，如果包括路径，请确保路径正确， 然
后再试一次。
所在位置 行:1 字符: 1
+ JEKYLL_ENV=production
+ ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ObjectNotFound: (JEKYLL_ENV=production:String) [], CommandNotFoundException
    + FullyQualifiedErrorId : CommandNotFoundException
```

#### 原因

这是 linux 环境设置环境变量的语法，我是在 windows 下执行的自然报错

#### 解决方案

windows 下 `powershell` 设置环境变量语法

```bash
$env:JEKYLL_ENV="production"
```
