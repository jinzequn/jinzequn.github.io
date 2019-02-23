---
title: Win10下编译TensorFlow源码
date: 2019-02-23 20:00:00
tags: TensorFLow
categories: TensorFlow
toc: True
---

在win10下用vs2015编译TensorFlow源码并进行测试.
<!--more-->

由于一些项目需要在windows下调用tensorflow模型,进行预测,所以接到在windows下运行模型的需求后,着手把目前在linux下的工程运行在windows下.然而在windows下编译TensorFlow是一个非常非常坑的事情,用了一个周末才编译出tensorflow.lib和tensorflow.dll这两个大宝贝,写下来记录一下.

##前期准备

1. Swig-3.0.12
2. Cmake
3. tensorflow源码 (r1.10.0)
4. Visual studio 2015 update3 (一定是这个版本)
5. Git
6. Shadowsocks(最好可以科学上网，因为有一些库要下)

##编译步骤

1. git config --global http.proxy 'socks5://127.0.0.1:1080' （开启科学上网光速通道，首先要有shadowsocks哈）
2. git clone -b v1.10.0 https://github.com/tensorflow/tensorflow.git
3. 用cmake-gui进行预编译，cmake平台选择要选x64
4. source code路径为 tensorflow\tensorflow\contrib\cmake
5. 在SWIG_EXECUTABLE选项中输入swig.exe的绝对路径，其他选择看需求
6. 用vs2015打开ALL_BUILD.vcxproj,  编译平台选择release,x64，编译ALL_BUILD
最好用内存超过20G的机器进行编译，否则可能会出现错误。编译成功会在Release目录下生成很多的库，其中包括tensorflow.lib和tensorflow.dll

##测试

测试项目为tensorflow给出的**Image Recognition Demo**，使用inception网络进行图片分类的demo，调用C++接口加载inception_v3_2016_08_28_froczen.pb模型，进行图片的分类

1. 下载模型文件
（https://storage.googleapis.com/download.tensorflow.org/models/inception_v3_2016_08_28_frozen.pb.tar.gz ）
2. 源码位于tensorflow\tensorflow\examples\label_image 下，新建vs2015工程，将main.cpp拷贝到工程目录下
3. 编写项目头文件，用于防止命名空间冲突，代码如下
```c++
	#define COMPILER_MSVC
	#define NOMINMAX  
```	
4. 右击项目→属性→配置属性→C/C++→常规→附加包含目录，输入如下所示的路径。
```
	E:\workspace\tensorflow\tensorflow
	E:\workspace\tensorflow\tensorflow\tensorflow\contrib\cmake\build
	E:\workspace\tensorflow\tensorflow\tensorflow\contrib\cmake\build\eigen\src\eigen
	E:\workspace\tensorflow\tensorflow\tensorflow\contrib\cmake\build\nsync\src\nsync\public
	E:\workspace\tensorflow\tensorflow\tensorflow\contrib\cmake\build\protobuf\src\protobuf\src
	%(AdditionalIncludeDirectories)
```
5. 右击项目，添加现有项，选择tensorflow\contrib\cmake\build\Release中的tensorflow.lib
6. Ctrl+F5 进行编译，注意其中测试图片和模型的路径问题需要按需调整，vs2015调试中过程中相对路径的根目录位于项目工程文件夹根目录中同名文件夹下，例如你的项目名为test，项目工程中会有一个名为test的文件夹。

##最后

编译的时候一定要有耐心, 并且c++的代码还是比较难看的,祝好运!