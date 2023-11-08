---
title: 使用C#(sharp)-OnnxRuntime(仅)来推理scrfd模型实现人脸检测
categories: [人脸识别]
tags: [csharp,scrfd,OnnxRuntime]
---
* [scrfd官网介绍文档](https://github.com/deepinsight/insightface/tree/master/detection/scrfd) 目前最优秀的人脸检测算法
* 使用高性能的[OnnxRuntime](https://onnxruntime.ai/)推理模型,告别臃肿的opencv库
* 使用[ImageSharp](https://github.com/SixLabors/ImageSharp)库来进行图片预处理(ps:图片预处理速度很慢各位有什么好的推荐下)

## 如何使用
已发布到[nuget](https://www.nuget.org/packages/FaceTrain.OnnxRuntime/) 源码在[github](https://github.com/laolaolulu/FaceTrain/tree/master/csharp/OnnxRuntime)
```bash
dotnet add package FaceTrain.OnnxRuntime
```
```csharp
//读取模型文件
var modelBytes = File.ReadAllBytes("Resource\\Model\\SCRFD\\scrfd_10g_kps.onnx");
//读取要检测的图片
var imgBytes = File.ReadAllBytes("Resource\\Img\\test.png");
//实例化网络
using var net = new FaceTrain.OnnxRuntime.SCRFD(modelBytes);
//执行推理拿到结果
var faces = net.Detection(imgBytes);
```
## 模型下载
官方模型是用pytorch框架训练的,需要使用[scrfd2onnx.py](https://github.com/deepinsight/insightface/blob/master/detection/scrfd/tools/scrfd2onnx.py)将模型转换下
作者已经转换好了可以直接下载使用

### scrfd2onnx踩坑经历

## 测试用例
[分别写了从摄像头获取图片以及从文件夹获取图片进行测试](https://github.com/laolaolulu/FaceTrain/blob/master/csharp/Tests/SCRFDTests.cs)

## 源码解读
1. 模型为[1,3,640,640]的输入,将图片等比例缩放
    ```csharp
    using Image<Rgb24> image = Image.Load<Rgb24>(img);
    //图片缩放
    image.Mutate(x =>
    {
        x.Resize(new ResizeOptions
        {
            Size = new Size(dimensions[2], dimensions[3]),
            Mode = ResizeMode.Pad,
        });
    });
    ```
2. Csharp使用以下代码来实现opencv的cv.blobFromImage函数

    ```csharp
    // opencv
    var blob = CvDnn.BlobFromImage(mat, 1 / 128.0, new Size(inputWidth, inputHeight), new Scalar(127.5, 127.5, 127.5), true, false);

    // csharp
    float mean = 127.5f, stddev = 128;
    image.ProcessPixelRows(accessor =>
    {
        for (int y = 0; y < accessor.Height; y++)
        {
            Span<Rgb24> pixelSpan = accessor.GetRowSpan(y);
            for (int x = 0; x < accessor.Width; x++)
            {
                processedImage[0, 0, y, x] = (pixelSpan[x].R - mean) / stddev;
                processedImage[0, 1, y, x] = (pixelSpan[x].G - mean) / stddev;
                processedImage[0, 2, y, x] = (pixelSpan[x].B - mean) / stddev;
            }
        }
    });
    ```
3. 结果去重(一张人脸会重复检测多次保留Score最高的哪个)
    ```csharp
    public static List<FaceBox> NMSBoxes(List<FaceBox> boxes, float overlapThreshold)
    {
        // 根据置信度对边界框进行排序（降序）
        boxes.Sort((box1, box2) => box2.Score.CompareTo(box1.Score));
        List<FaceBox> selectedBoxes = new();
        while (boxes.Count > 0)
        {
            FaceBox currentBox = boxes[0];
            selectedBoxes.Add(currentBox);
            boxes.RemoveAt(0);
            float areaCurrent = (currentBox.Right - currentBox.Left) * (currentBox.Bottom - currentBox.Top);
            for (int i = 0; i < boxes.Count; i++)
            {
                FaceBox candidateBox = boxes[i];

                float x1 = Math.Max(currentBox.Left, candidateBox.Left);
                float y1 = Math.Max(currentBox.Top, candidateBox.Top);
                float x2 = Math.Min(currentBox.Right, candidateBox.Right);
                float y2 = Math.Min(currentBox.Bottom, candidateBox.Bottom);

                float intersectionArea = Math.Max(0, x2 - x1) * Math.Max(0, y2 - y1);

                float IoU = intersectionArea / (areaCurrent + (candidateBox.Right - candidateBox.Left) * (candidateBox.Bottom - candidateBox.Top) - intersectionArea);
                if (IoU > overlapThreshold)
                {
                    boxes.RemoveAt(i);
                    i--;
                }
            }
        }
        return selectedBoxes;
    }
    ```
