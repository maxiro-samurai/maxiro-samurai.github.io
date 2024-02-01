---
title: Nipype（二）：showcase
tags:
  - 神经影像
categories:
  - python
date: 2023-07-24 17:13:52
---


# Nipype Showcase

使用简单的fMRI预处理流程来展示Workflow：

1. slice time correction
2. motion correction
3. smoothing 

## 准备预处理workflow

首先导入Nipype包中的Node和Workflow

    from nipype import Node, Workflow
接着导入预处理需要的interfaces

    from nipype.interfaces.fsl import SliceTimer, MCFLIRT, Smooth

接着把这三个接口接入Node并且定义输入

    # 初始化slicetime节点
    slicetimer = Node(SliceTimer(index_dir=False,
                                interleaved=True,
                                time_repetition=2.5),
                      name="slicetimer")

    # 初始化运动矫正节点
    mcflirt = Node(MCFLIRT(mean_vol=True,
                          save_plots=True),
                  name="mcflirt")
    # 初始化光滑节点
    smooth = Node(Smooth(fwhm=4), name="smooth")
接下来就可以创建一个workflow，把这三个节点连接起来

    # 创建预处理workflow
    preproc01 = Workflow(name='preproc01', base_dir='.')

    # 连接节点到workflow中
    preproc01.connect([(slicetimer, mcflirt, [('slice_time_corrected_file', 'in_file')]),
                   (mcflirt, smooth, [('out_file', 'in_file')])])

可以把workflow可视化

    # 生成可视化图片
    preproc01.write_graph(graph2use='orig')

    # 导入Ipython的图像包
    from IPython.display import Image
    Image(filename="preproc01/graph_detailed.png")
<div>			<!--块级封装-->
    <center>	<!--将图片和文字居中-->
    <img src="https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.50b24pi7e1c0.webp"
        alt="寄"
        style="zoom:这里写图片的缩放百分比"/>
    <br>		<!--换行-->
    结果<!--标题-->
    </center>
</div>
 
## 在一个功能图像上运行workflow
创建过一个workflow后，开始在功能像上运行。首先要确定数据文件的路径。

    slicetimer.inputs.in_file = '/data/ds000114/sub-01/ses-test/func/sub-01_ses-test_task-fingerfootlips_bold.nii.gz'

data文件夹下有所需要的数据。

使用Nipype的并行处理功能，观察处理时间

    %time preproc01.run('MultiProc', plugin_args={'n_procs': 5})
<div>			<!--块级封装-->
    <center>	<!--将图片和文字居中-->
    <img src="https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/1690162024205.2r3uycfb2ui0.webp"
        alt="寄"
        style="zoom:这里写图片的缩放百分比"/>
    <br>		<!--换行-->
    <!--标题-->
    </center>
</div>

<div>			<!--块级封装-->
    <center>	<!--将图片和文字居中-->
    <img src="https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/1690161973043.4l1flbmece00.webp"
        alt="寄"
        style="zoom:这里写图片的缩放百分比"/>
    <br>		<!--换行-->
    结果<!--标题-->
    </center>
</div>

上图 输出了整个Workflow的工作流程，最后可以看到整个处理花费大概2min。

## 结果
检查一下输出文件夹有什么。

    !tree preproc01 -I '*js|*json|*pklz|_report|*.dot|*html'

<div>			<!--块级封装-->
    <center>	<!--将图片和文字居中-->
    <img src="https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.2li3usdff6a0.webp"
        alt="寄"
        style="zoom:这里写图片的缩放百分比"/>
    <br>		<!--换行-->
    <!--标题-->
    </center>
</div>


## 修改参数重新运行

    smooth.inputs.fwhm = 2
    %time preproc01.run('MultiProc', plugin_args={'n_procs': 5})

![image](https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.vmg63btwdh.webp)  

可看到时间仅用14秒，因为整个workflow不会重新运行一遍，只把更改参数的Node运行，在这里就是把smooth重新运行。


## 并行处理

preproc1预处理workflow使用了大概两分钟，如果我要处理5张功能像图片，他会花费10分钟。

首先我们复制5个workflow

    # First, let's copy/clone 'preproc01'
    preproc02 = preproc01.clone('preproc02')
    preproc03 = preproc01.clone('preproc03')
    preproc04 = preproc01.clone('preproc04')
    preproc05 = preproc01.clone('preproc05')

我们想要并行处理，需要把他们整合在一个workflow中。

    metaflow = Workflow(name='metaflow', base_dir='.')
    # Now we can add the five preproc workflows to the bigger metaflow
    metaflow.add_nodes([preproc01, preproc02, preproc03,
                    preproc04, preproc05])


可视化看一下整个workflow

    # As before, let's write the graph of the workflow
    metaflow.write_graph(graph2use='flat')

    Image(filename="metaflow/graph_detailed.png")

![image](https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.2cmsdo1mdt8g.webp)

使用并行处理

    %time metaflow.run('MultiProc', plugin_args={'n_procs': 5})

log信息此处省略
![image](https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.3zmgk47a2ji0.webp)

可以看到总共用时2min，这就是使用Nipype的原因。

## metaflow的结果

    !tree metaflow -I '*js|*json|*pklz|_report|*.dot|*html'

![image](https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.2kua1bpzkk80.webp)


## 参考

nipype官方教程 [Nipype Showcase](https://miykael.github.io/nipype_tutorial/notebooks/introduction_showcase.html)