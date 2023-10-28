---
title: 使用Emscripten将c++代码编译到js(WebAssembly)
categories: [人脸识别]
tags: [opencv,emscripten,onnx]
---

### 使用 Emscripten 编译为 OpenCV.js(wasm);在前端利用opencv实现神经网络模型(onnx)推理
#### 编译OpenCV.js
*注意用 linux 环境,windows 下各种错误我已放弃*
* [Emscripten官方安装文档](https://emscripten.org/docs/getting_started/downloads.html)
* [OpenCV.js官方编译文档](https://docs.opencv.org/4.8.0/d4/da1/tutorial_js_setup.html)
  
```shell
# Get the emsdk repo
git clone https://github.com/emscripten-core/emsdk.git

# Enter that directory
cd emsdk

# Fetch the latest version of the emsdk (not needed the first time you clone)
git pull

# Download and install the latest SDK tools.
./emsdk install latest

# Make the "latest" SDK "active" for the current user. (writes .emscripten file)
./emsdk activate latest

# Activate PATH and other environment variables in the current terminal
source ./emsdk_env.sh

# 拉取opencv代码仓库
git clone https://github.com/opencv/opencv.git

# 编译opencv.js 很慢需要等一个小时以上
emcmake python3 ./opencv/platforms/js/build_js.py build_js
```

* 安装完成后每次重启终端都需要执行 `source ./emsdk_env.sh` 
* 既然是自行编译那当然要高度定制化了,`opencv_js.config.py`这个配置文件中你不需要使用的函数都可以删掉然后再编译大概能压缩到`2M`左右
##### 错误处理
1. `./emsdk: /bin/sh^M: bad interpreter: No such file or directory`
    我使用的 windows 下 git clone 的 emsdk,然后再 ubuntu 下执行就报这个错误,应该是 windows 下拉取的文件的结尾换行与 linux 下的不一样导致;去 linux 环境下重新 git clone 就好了
2. `'emcmake' launcher or specify it either by EMSCRIPTEN environment variable or --emscripten_dir option.`
   请手动配置下环境变量 `environment`注意不是emsdk目录
    ```shell
    export EMSCRIPTEN=/mnt/d/lipeng/emsdk/upstream/emscripten
    ```
3. `build_js.py: error: unrecognized arguments: -DCMAKE_TOOLCHAIN_FILE=/mnt/d/lipeng/emsdk/upstream/emscripten/cmake/Modules/Platform/Emscripten.cmake -DCMAKE_CROSSCOMPILING_EMULATOR=/mnt/d/lipeng/emsdk/node/16.20.0_64bit/bin/node`  
    
      请在`platforms\js\build_js.py`文件中添加以下代码
      ```python
      parser.add_argument("--webnn", action="store_true", help="Enable WebNN Backend")
      + parser.add_argument("-DCMAKE_TOOLCHAIN_FILE", default="", help="DCMAKE_TOOLCHAIN_FILE")
      + parser.add_argument("-DCMAKE_CROSSCOMPILING_EMULATOR",default="",help="DCMAKE_CROSSCOMPILING_EMULATOR")   
      args = parser.parse_args()    
      log.debug("Args: %s", args)
      ```
    
#### 推理onnx模型
一些轻量级的频繁推理模型建议放前端客户端进行计算,可以节约图片频繁传递到后端导致流量带宽的巨大消耗;例如人脸识别功能,这里使用opencv.js来推理scrfd;
官方`scrfd`代码推理中用了网络对象中的`getUnconnectedOutLayersNames`这个函数,而opencv.js编译配置文件中没有这个函数,需要再配置文件`opencv_js.config.py`中添加进去重新编译
```python
"dnn_Net": [
        "setInput",
        "forward",
        "setPreferableBackend",
      + "getUnconnectedOutLayersNames",
    ],
```

##### opencv.js编译`getUnconnectedOutLayersNames`过程中错误处理
1. error: no member named 'vectorstd' in namespace 'std'
   这个错误有点像是字符串拼接出了问题,把本应该是`vector<std::string>`转换成了`vectorstd::string`,尖括号被转没了;于是我在源码里找呀找到处debug,终于找到了一处`modules\js\generator\embindgen.py`修改了下
   ```python
   for key in type_dict:
                if key in ret_type:
                    - ret_type = re.sub('(^|[^\w])' + key + '($|[^\w])', type_dict[key], ret_type)
                    + ret_type = re.sub(r"\b" + key + r"\b", type_dict[key], ret_type)
   ```

2. 编译通过了但调用发生了异常`"Cannot call dnn_Net.getUnconnectedOutLayersNames due to unbound types: NSt3__26vectorINS_12basic_stringIcNS_11char_traitsIcEENS_9allocatorIcEEEENS4_IS6_EEEE"`
需要在`modules\js\src\core_bindings.cpp`绑定个类型

    ```c++
    register_vector<float>("FloatVector");
    register_vector<double>("DoubleVector");
    + register_vector<std::string>("StringVector");
    register_vector<cv::Point>("PointVector");
    ```

简单写几句代码
```js
//先根据模型训练时提供的图片进行预处理(步骤省略)
const blob = cv.blobFromImage(dst, 1.0 / 128, inpSize, new cv.Scalar(127.5, 127.5, 127.5), true);
const name = 'scrfd.onnx';
cv.FS_createDataFile('/', name, new Uint8Array(onnxdata), true, false);
const netScrfd = cv.readNetFromONNX(name);//加载模型
const net = netScrfd.setInput(blob);
const vnames = netScrfd.getUnconnectedOutLayersNames();
const names = Array.from({ length: vnames.size() }, (_, index) => vnames.get(index));
const outs = new cv.MatVector();
netScrfd.forward2(outs, names);
//outs返回值根据模型的输出数据结构解析就对了
```
