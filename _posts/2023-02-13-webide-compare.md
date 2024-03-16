---
title: 2023年各大厂WebIDE(CloudStudio、DevStudio、Codespaces)使用对比
img_path: /assets/img/posts/20230213/
categories: [短中取长]
tags: [WebIDE, CloudStudio, DevStudio, Codespaces]
---

本人抱着颗白嫖的心态撸了一遍各大厂的 WebIDE(CloudStudio、DevStudio、Codespaces) 工具,现分享下白嫖经验

## 腾讯 CloudStudio

_<https://cloudstudio.net/>_

- 最花里胡哨的工具,建库很多模板有不同的操作系统(Red Ubuntu),模板没尝试完...
- 该有的环境常用的工具都安装好了(最新长期稳定版)
- ~~每月能白嫖 1000 分钟(所有仓库空间共用),然后 1 毛钱一分钟;包月 300~~
- 每月能白嫖3000分钟了，每天基本上可以用2小时挺厚道的

![addremote](cloudstudio-index.webp){:.normal}

![addremote](cloudstudio-code.webp){:.normal}

![addremote](cloudstudio-cost.webp){:.normal}

## ~~阿里 DevStudio~~ 太监了

_~~<https://ide.aliyun.com/>~~_

- 云效 DevStudio 已于2023年12月15日停止服务
- ~~目前为止处于测试阶段可以完全白嫖但不知道正式版收费如何~~
- ~~有完善的 java 开发环境~~
- ~~环境配置不完善,node 默认还是 12(自带版本管理工具),很多开发环境需要自行安装~~
- ~~空间停止(会自动)后重新进入你的任何配置都还原了~~

![addremote](devstudio-index.webp){:.normal}

![addremote](devstudio-code.webp){:.normal}

## 华为 CloudIDE

_<https://devcloud.cn-north-4.huaweicloud.com/cloudide>_

- ~~免费开通,结果我点了提示余额不足;我这个白嫖党为了感受华为带来的神秘感特意充值 1 元,结果还是余额不足放弃体验......~~
- 免费体验：
    * 每个实例可使用时长为60分钟，超时后实例释放且数据清除；
    * 每日累计免费使用时长120分钟，余量少于5分钟不能创建新实例；
- 收费标准：
    * 鲲鹏计算large:¥1.80/小时
    * x86计算资源medium：¥1.20/小时
    * x86存储资源:¥0.00098/GB/小时
- 皮肤看上去与其他有点不太一样，可能是自研力高一点
- 还是可以白嫖的，虽然60分钟会释放但基本上环境都有

![addremote](cloudide.webp){:.normal}

![addremote](cloudide-index.webp){:.normal}

## 微软 Codespaces

_<https://github.com/codespaces>_

- 只能使用 github 仓库初始化(进去用 git 想怎么提交都行)
- ~~每月免费 60 小时,超出后 0.18 美元一小时 (1.23 元)~~
- 每月免费2000分钟 [收费标准详情](https://docs.github.com/en/billing/managing-billing-for-github-actions/about-billing-for-github-actions)
- 开发环境全是最新
- 基本上不需要做任何配置就能进行开发

![addremote](codespaces-index.webp){:.normal}

![addremote](codespaces-code.webp){:.normal}

![addremote](codespaces-cost.webp){:.normal}

## 总结

只体验了一遍通用开发环境,用 vs code 的推荐上手;没有完全白嫖的,建议国内大厂出个开源免费使用模式:例如使用自家的 git 仓库,开源库可免费使用;或者放大免费使用时间平均每天免费 4 小时左右...
