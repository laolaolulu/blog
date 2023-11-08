---
title: jekyll-theme-chirpy主题文章最后更新时间为空怎么回事
categories: [错误笔记]
tags: [jekyll-theme-chirpy, last_modified_at]
---

### `jekyll-theme-chirpy`主题官方示例是有最后更新时间的，自己发布后最后更新时间却没有

## 原因

官方示例文章的最后更新时间`last_modified_at`是通过 github 的 workflows 来生成的，自己却用的`jekyll b`命令来编译的

## 解决方案

手动安装插件`jekyll-last-modified-at`：在`Gemfile`文件中添加代码

```bash
group :jekyll_plugins do
  gem "jekyll-last-modified-at"
end
```

执行`bundle`安装一下，然后再执行`jekyll b`编译文章就有最后更新时间了
