---
title: C#(sharp)人脸识别
categories: [人脸识别]
tags: [OpenCvSharp4]
---

### 错误笔记

1. System.TypeInitializationException:“The type initializer for 'OpenCvSharp.Internal.NativeMethods' threw an exception.”
DllNotFoundException: Unable to load DLL 'OpenCvSharpExtern' or one of its dependencies: 找不到指定的模块。 (0x8007007E)
> 使用`OpenCvSharp4`还需要安装一个(OpenCvSharp4.runtime.\*)这个库,如果你是 windows 环境下运行就安装`OpenCvSharp4.runtime.win`
1. Mat [ 0*0*CV_8UC1, IsContinuous=False, IsSubmatrix=False, Ptr=0x2356878e5c0, Data=0x0
> 请注意如果你传入的图片路径无效那么读取时不会报错,返回的 Mat 对象的 Data 为 0
