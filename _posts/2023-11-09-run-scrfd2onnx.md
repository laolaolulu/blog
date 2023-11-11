---
title: 将scrfd模型转换为onnx成功历程
categories: [人脸识别]
tags: [scrfd]
---
[scrfd官方文档](https://github.com/deepinsight/insightface/tree/master/detection/scrfd)   ,  作者转换好了点击去[下载](../face-model-download)

python世界发展过快,版本之间依赖不兼容问题很是头疼;避免这种问题浪费时间笔者总结了以下六点

......

---

* 不要相信自己搭的绝配环境,来装个[mini conda](https://docs.conda.io/projects/miniconda/en/latest/)随意折腾,听人劝吃饱饭.乖~
  ```bash
  # conda常用命令

  # 创建环境
  conda create --name test python==3.9.18
  # 进入创建的环境
  conda activate test
  # 查看自己创建了多少环境
  conda env list
  # 删除不要的环境
  conda env remove --name test
  ```
* 成功步骤环境:安装时提示缺少什么包就安装
  ```bash
  # 安装pytorch
  conda install pytorch==1.9.1 torchvision==0.10.1 torchaudio==0.9.1 cpuonly -c pytorch
  # 安装mmcv
  pip install -U openmim
  mim install mmcv-full==1.3.3
  # 这步可能会出现错误:Failed building wheel for mmpycocotools 安装以下包版本
  pip install cython==0.29.36

  # 目录下 detection/scrfd 
  pip install -r requirements/build.txt
  pip install -v -e .

  # 接下来就是转换了 需要个input-img不知道用来干什么
  python tools/scrfd2onnx.py configs/scrfd/scrfd_500m.py SCRFD_500M.pth --shape 640 640 --input-img test.png

  # kps后缀的模型是返回检测5个器官点(眼鼻嘴),configs哪个路径下的.py文件区别就是 use_kps=True
  # 含bg字符的模型对于我这种菜鸡是搞不懂区别的,反正我发现的规律就是kps用的bn;代码对比的区别:
  # norm_cfg=dict(type='GN', num_groups=8, requires_grad=True),
  # strides_share=True,
  # norm_cfg=dict(type='BN', requires_grad=True),
  # strides_share=False,
  ```
* 有时候在windows下把自己心态搞炸了可以换个姿势到linux下,推荐使用wsl文件都给你映射好了在`/mnt`
* 安装依赖包各种错误从开源库中去看引用版本,没看到各依赖包版本时就查看最后更新时间,通过时间去找版本
