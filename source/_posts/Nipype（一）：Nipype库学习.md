---
title: 神经影像（一）：Nipype库学习
tags:
  - 神经影像
categories:
  - python
date: 2023-07-21 15:41:33
---


# Nipype简介

Nipype是一个开源神经影像处理软件包，使用python编写。整合了传统神经影像处理软件包的接口（SPM,FSL,FreeSurfer,AFNI以及其他），可以使研究人员通过不同的软件处理图像，将其整合在一起。

比如你想使用SPM做头动矫正，FreeSurfer做配准，ANTS用来标准化，然后FSL做平滑。使用传统流程会非常麻烦，需要你对各种软件的掌握。使用Nipype可以轻松完成。


## 什么是Nipype

Nipype由多个组件构成，主要的组件有Interfaces,the WorkflowEngine 和Execution Plugins:

<br/><br>
<div>			<!--块级封装-->
    <center>	<!--将图片和文字居中-->
    <img src="https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/1689908081549.23zqnq9rfyxs.webp"
        alt="寄"
        style="zoom:这里写图片的缩放百分比"/>
    <br>		<!--换行-->
    Nipype构成	<!--标题-->
    </center>
</div>
<br><br> 

* Interface: 相关软件的接口，可以是一个函数
* Node/MapNode: 把一个接口打包成节点，在Workflow中使用。
* Workflow: 一个表示工作流程的图，一个Workflow下面有很多节点。

* Plugin: 用来展示Workflow是如何工作的


## Docker 

Docker是一个开源项目，用来在容器中自动部署应用。（Docker的使用和学习令写，在这只简单介绍）这个容器可以打包运行Nipype所需的软件和完整的文件系统：代码，系统工具，软件库，比如 Python, FSL, AFNI, SPM, FreeSurfer, ANTs。这保证了可移植性，在不同环境中都可以运行。

Nipype官方教程所使用的  [miykael/nipype_tutorial](https://hub.docker.com/r/miykael/nipype_tutorial/)镜像，建立了一个Linux环境和一系列所需软件包。同样的我们可以创建自己的docker镜像处理自己的数据。（之后再学用[ Neurodocker](https://github.com/kaczmarj/neurodocker)创建）

### Docker for windows

我是用的是Windows 10去docker官网下载 [Docker for windows](https://docs.docker.com/toolbox/toolbox_install_windows/)

下载完成后使用终端测试是否安装正确：

    docker --version
<div>			<!--块级封装-->

<img src="https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.12aigggbs7yo.webp"
    alt="寄"
    style="zoom:这里写图片的缩放百分比"/>
<br>		<!--换行-->
	<!--标题-->
    
</div>


    docker run hello-world
<div>			<!--块级封装-->
    <center>	<!--将图片和文字居中-->
    <img src="https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.50rb0914t0w0.webp"
        alt="寄"
        style="zoom:这里写图片的缩放百分比"/>
    <br>		<!--换行-->
    	<!--标题-->
    </center>
</div>


这里提醒一下，Docker默认下载到C盘无法修改，Docker拉取的镜像也是在C盘，所以需要C盘空间很大，我试了修改镜像的位置，行不通，所以只能扩充C盘的大小了。

### 拉取镜像

确认Docker安装没有问题后，就可以开始拉取镜像了。

    docker pull miykael/nipype_tutorial:latest

由于是从国外的镜像网站拉取镜像，速度很慢。解决方法是使用国内镜像加速。


找到C:\Program Files\Docker\Docker\resources文件夹下的windows-daemon-options.json文件

    "registry-mirrors": ["https://m3e4jmm0.mirror.aliyuncs.com"],

把镜像地址改为阿里云镜像。


### 运行镜像

简单运行miykael/nipype_tutorial，使用命令：

    docker run -it --rm -p 8888:8888 miykael/nipype_tutorial jupyter notebook

如果想要使用本地的dataset，使用命令：

    docker run -it --rm -v /path/to/nipype_tutorial/:/home/neuro/nipype_tutorial -v /path/to/data/:/data -v /path/to/output/:/output -p 8888:8888 miykael/nipype_tutorial jupyter notebook

* -it 表示打开一个可以交互的容器
* --rm 在关闭Docker后容器自动移除
* -p 表示使用哪个端口
* -v 表示把本地的文件加载到容器中去 /path/to/nipype_tutorial/ 表示本地nipype_tutorial路径，：表示加载到容器中的路径。

* miykael/nipype_tutorial 表示运行哪个镜像
* jupyter notebook 在容器中打开jupyter

### 关闭容器

在终端使用ctrl-c可以关闭容器

### 列出所有image

    docker images

### 删除镜像

    docker rmi -f 7d9495d03763
后面的数字为镜像ID

### 提取和加载镜像
如果想使用U盘保存镜像，使用命令：

    # Export docker image miykael/nipype_tutorial
    docker save -o nipype_tutorial.tar miykael/nipype_tutorial

    # Import docker image on another PC
    docker load --input nipype_tutorial.tar

## 数据保存格式

这个教程的数据保存结构是根据[ Brain Imaging Data Structure (BIDS)](http://bids.neuroimaging.io/).

这个格式结构清晰，并且易于共享。

<div>			<!--块级封装-->
    <center>	<!--将图片和文字居中-->
    <img src="https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.hbe4ctu09jk.webp"
        alt="寄"
        style="zoom:这里写图片的缩放百分比"/>
    <br>		<!--换行-->
    	<!--标题-->
    </center>
</div>
<br> 


## 参考

nipype官方教程 [Nipype tutorial](https://miykael.github.io/nipype_tutorial/)












