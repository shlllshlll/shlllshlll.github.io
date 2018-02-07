---
title: 学习OpenCV--如何在Visual Studio中使用OpenCV
date: 2018-02-06 16:44:47
tags:
---
>原文地址： [How to build applications with OpenCV inside the "Microsoft Visual Studio" ](https://docs.opencv.org/master/dd/d6e/tutorial_windows_visual_studio_opencv.html)  
>我的博客： [SHLLL的小站](http://shlll.me) \ [Github](https://shlllshlll.github.io/) \ [CSDN](http://blog.csdn.net/u011880112) \ [博客园](http://www.cnblogs.com/shlll/) \ [简书](https://www.jianshu.com/u/cbf8b521f6c2)

## 前言：

OpenCV是一个开源的跨平台计算机视觉库，基于C和C++语言开发，同时也提供了Python、Java和Javascript等语言的绑定。OpenCV中包含了大量关于计算机视觉、模式识别和图像处理的API函数库。下面就来介绍一下OpenCV在Windows平台下Visual Studio环境中的安装、环境变量配置以及VS中如何进行配置。本文依据官方文档整理翻译而成。

## 一、准备工作

首要的准备工作当然是安装OpenCV和Visual Studio这两个软件，在安装上并没有什么难度，我们可以直接在[OpenCV官网](https://opencv.org/releases.html)中选择对应版本的Win Pack下载安装即可。而Visual Stuido安装也很简单，直接下载相应的安装包安装即可，在这里就不再赘述了。

## 二、OpenCV环境变量配置

在完成软件安装之后，我们还需要几个步骤才能在VS中使用OpenCV进行开发。在我的电脑->属性->高级系统设置->环境变量中，我们首先新建一个系统变量“OPENCV_DIR”，它的值则指向OpenCV的安装目录。

![OpenCV安装目录](https://i.loli.net/2018/02/06/5a799b2109197.png)

![OPENCV_DIR环境变量](https://i.loli.net/2018/02/06/5a799b4f73bce.png)

*注意：*目录中的VC15要和我们所安装的Visual Studio版本相匹配，由于博主安装的VS为2017版。因此其使用的VC编译器版本号为15，而加入我们使用的是VS2015，那么我们就需要将上述图片中的目录改为VC14。

*注意2：*这里我们首先创建了一个“OPENCV_DIR”变量来指向OpenCV的安装目录，而其它网上流传的配置教程大多没有这步，但是这样做的好处是明显的，因为我们后面对Path环境变量以及VS设置中的配置都引用我们上面设置的“OPENCV_DIR”变量作为安装目录。如此一来，当我们后面需要升级或者更改OpenCV目录的时候，我们只需要更改“OPENCV_DIR”变量的路径即可，不需要再进行繁杂的设置。

接下来我们在Path环境变量中添加一个项“%OPENCV_DIR%\bin”，到这里，我们的环境变量配置就大功告成了。

![Path环境变量设置](https://i.loli.net/2018/02/06/5a79b6999a920.png)

## 三、VS配置

### 1、首先新建一个控制台工程

如图所示，我们新建一个空白的控制台工程

![新建工程](https://i.loli.net/2018/02/06/5a79b77468741.png)

### 2、打开属性管理器

接下来我们在Solution Explorer中找到我们新建的项目，然后右键弹出菜单中，我们找到最后一项，即Properties。

![属性管理器](https://i.loli.net/2018/02/06/5a79b871e0e46.png)

![工程属性窗口](https://i.loli.net/2018/02/06/5a79b90e868ab.png)

找到C++设置组点击General，然后我们找到Additional Include Directories来设置我们OpenCV的Include路径，我们将其设置为“$(OPENCV_DIR)\..\..\include”，这里的“$(OPENCV_DIR)”就是引用自我们在系统环境变量里设置的OPENCV_DIR。

![设置Include路径](https://i.loli.net/2018/02/06/5a79bb983cb48.png)

接下来我们找到Linker->General然后找到“Additional Library Directories”选项，我们将其设置为“$(OPENCV_DIR)\lib”.

![设置Lib路径](https://i.loli.net/2018/02/06/5a79bb9727ca6.png)

最后，我们还要向连接器指定我们要使用的OpenCV动态链接库，也就是在Linker->Input中，找到“Additional Dependencies”，然后添加我们要使用的库，即在Release中我们需要“opencv_world340.lib”，而在Debug模式下我们需要“opencv_world340d.lib”。

![设置Lib依赖](https://i.loli.net/2018/02/06/5a79bc04561dd.png)


至此我们就完成了Opencv在Windows平台上的配置，接下来就可以愉快的使用OpenCV进行开发啦。

## 四、进行简单的测试

我们完成了配置就可以使用OpenCV来进行一个简单的小测试了。

```cpp
#include "stdafx.h"
#include <opencv2/core.hpp>
#include <opencv2/imgcodecs.hpp>
#include <opencv2/highgui.hpp>
#include <iostream>

using namespace cv;
using namespace std;

int main(int argc, char** argv)
{
    String filename = (argc >= 2) ? argv[1] : "../data/lena.jpg";

    Mat image;
    image = imread(filename, IMREAD_COLOR); // Read the file

    if (image.empty()) // Check for invalid input
    {
        cout << "Could not open or find the image" << std::endl;
        return -1;
    }

    namedWindow("Display window", WINDOW_AUTOSIZE); // Create a window for display.
    imshow("Display window", image); // Show our image inside it.

    waitKey(0); // Wait for a keystroke in the window
    return 0;
}
```

以上代码就是利用OpenCV打开一个图片，效果如下：

![程序效果图](https://i.loli.net/2018/02/06/5a79c231e724a.png)
