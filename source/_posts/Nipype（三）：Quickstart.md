---
title: Nipype（三）:Quickstart
tags:
  - 神经影像
categories:
  - python
date: 2023-07-24 17:15:17
---


# Nipype Quickstart

首先需要Import所需要的函数和包

    import os 
    from os.path import abspath

    from nipype import Workflow, Node, MapNode, Function
    from nipype.interfaces.fsl import BET, IsotropicSmooth, ApplyMask #fsl的接口函数

    from nilearn.plotting import plot_anat #画图
    %matplotlib inline #在Jupyter Notebook中画图时直接嵌入Notebook单元格中
    import matplotlib.pyplot as plt

## Interfaces

接口是Nipype的核心。这些接口是python接口可以通过这些接口访问外部软件包，这些软件包大多不是用python写的。（比如FSL,SPM和FreeSurfer）

使用FSL中的bet()函数

    # will use a T1w from ds000114 dataset

    # input_file是BET()函数的输入参数，为数据集的路径
    input_file =  abspath("/data/ds000114/sub-01/ses-test/anat/sub-01_ses-test_T1w.nii.gz")
    # BET()实例化
    bet = BET()

    bet.inputs.in_file = input_file
    # 输出为输出路径
    bet.inputs.out_file = "/output/T1w_nipype_bet.nii.gz"
    res = bet.run()

画出输出图像

    plot_anat('/output/T1w_nipype_bet.nii.gz', 
          display_mode='ortho', dim=-1, draw_cross=False, annotate=False);

![image](https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.6a3xqvkyprk0.webp)


使用help指令可以看接口函数的使用方法

    BET.help()

    Wraps the executable command ``bet``.

    FSL BET wrapper for skull stripping

    For complete details, see the `BET Documentation.
    <https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/BET/UserGuide>`_

    Examples
    --------
    >>> from nipype.interfaces import fsl
    >>> btr = fsl.BET()
    >>> btr.inputs.in_file = 'structural.nii'
    >>> btr.inputs.frac = 0.7
    >>> btr.inputs.out_file = 'brain_anat.nii'
    >>> btr.cmdline
    'bet structural.nii brain_anat.nii -f 0.70'
    >>> res = btr.run() # doctest: +SKIP

    Inputs::

            [Mandatory]
            in_file: (a pathlike object or string representing an existing file)
                    input file to skull strip
                    argument: ``%s``, position: 0

            [Optional]
            out_file: (a pathlike object or string representing a file)
                    name of output skull stripped image
                    argument: ``%s``, position: 1
            outline: (a boolean)
                    create surface outline image
                    argument: ``-o``
            mask: (a boolean)
                    create binary mask image
                    argument: ``-m``
            skull: (a boolean)
                    create skull image
                    argument: ``-s``
            no_output: (a boolean)
                    Don't generate segmented output
                    argument: ``-n``
            frac: (a float)
                    fractional intensity threshold
                    argument: ``-f %.2f``
            vertical_gradient: (a float)
                    vertical gradient in fractional intensity threshold (-1, 1)
                    argument: ``-g %.2f``
            radius: (an integer)
                    head radius
                    argument: ``-r %d``
            center: (a list of at most 3 items which are an integer)
                    center of gravity in voxels
                    argument: ``-c %s``
            threshold: (a boolean)
                    apply thresholding to segmented brain image and mask
                    argument: ``-t``
            mesh: (a boolean)
                    generate a vtk mesh brain surface
                    argument: ``-e``
            robust: (a boolean)
                    robust brain centre estimation (iterates BET several times)
                    argument: ``-R``
                    mutually_exclusive: functional, reduce_bias, robust, padding,
                    remove_eyes, surfaces, t2_guided
            padding: (a boolean)
                    improve BET if FOV is very small in Z (by temporarily padding end
                    slices)
                    argument: ``-Z``
                    mutually_exclusive: functional, reduce_bias, robust, padding,
                    remove_eyes, surfaces, t2_guided
            remove_eyes: (a boolean)
                    eye & optic nerve cleanup (can be useful in SIENA)
                    argument: ``-S``
                    mutually_exclusive: functional, reduce_bias, robust, padding,
                    remove_eyes, surfaces, t2_guided
            surfaces: (a boolean)
                    run bet2 and then betsurf to get additional skull and scalp surfaces
                    (includes registrations)
                    argument: ``-A``
                    mutually_exclusive: functional, reduce_bias, robust, padding,
                    remove_eyes, surfaces, t2_guided
            t2_guided: (a pathlike object or string representing a file)
                    as with creating surfaces, when also feeding in non-brain-extracted
                    T2 (includes registrations)
                    argument: ``-A2 %s``
                    mutually_exclusive: functional, reduce_bias, robust, padding,
                    remove_eyes, surfaces, t2_guided
            functional: (a boolean)
                    apply to 4D fMRI data
                    argument: ``-F``
                    mutually_exclusive: functional, reduce_bias, robust, padding,
                    remove_eyes, surfaces, t2_guided
            reduce_bias: (a boolean)
                    bias field and neck cleanup
                    argument: ``-B``
                    mutually_exclusive: functional, reduce_bias, robust, padding,
                    remove_eyes, surfaces, t2_guided
            output_type: ('NIFTI' or 'NIFTI_PAIR' or 'NIFTI_GZ' or
                    'NIFTI_PAIR_GZ')
                    FSL output type
            args: (a string)
                    Additional parameters to the command
                    argument: ``%s``
            environ: (a dictionary with keys which are a bytes or None or a value
                    of class 'str' and with values which are a bytes or None or a
                    value of class 'str', nipype default value: {})
                    Environment variables

    Outputs::

            out_file: (a pathlike object or string representing a file)
                    path/name of skullstripped file (if generated)
            mask_file: (a pathlike object or string representing a file)
                    path/name of binary brain mask (if generated)
            outline_file: (a pathlike object or string representing a file)
                    path/name of outline file (if generated)
            meshfile: (a pathlike object or string representing a file)
                    path/name of vtk mesh file (if generated)
            inskull_mask_file: (a pathlike object or string representing a file)
                    path/name of inskull mask (if generated)
            inskull_mesh_file: (a pathlike object or string representing a file)
                    path/name of inskull mesh outline (if generated)
            outskull_mask_file: (a pathlike object or string representing a file)
                    path/name of outskull mask (if generated)
            outskull_mesh_file: (a pathlike object or string representing a file)
                    path/name of outskull mesh outline (if generated)
            outskin_mask_file: (a pathlike object or string representing a file)
                    path/name of outskin mask (if generated)
            outskin_mesh_file: (a pathlike object or string representing a file)
                    path/name of outskin mesh outline (if generated)
            skull_mask_file: (a pathlike object or string representing a file)
                    path/name of skull mask (if generated)
            skull_file: (a pathlike object or string representing a file)
                    path/name of skull file (if generated)

    References:
    -----------
    BibTeX('@article{JenkinsonBeckmannBehrensWoolrichSmith2012,author={M. Jenkinson, C.F. Beckmann, T.E. Behrens, M.W. Woolrich, and S.M. Smith},title={FSL},journal={NeuroImage},volume={62},pages={782-790},year={2012},}', key='JenkinsonBeckmannBehrensWoolrichSmith2012')
​
使用FSL中的光滑函数，高斯核为4mm

    smoothing = IsotropicSmooth()
    smoothing.inputs.in_file = "/data/ds000114/sub-01/ses-test/anat/sub-01_ses-test_T1w.nii.gz"
    smoothing.inputs.fwhm = 4
    smoothing.inputs.out_file = "/output/T1w_nipype_smooth.nii.gz"
    smoothing.run()
    # plotting the output
    plot_anat('/output/T1w_nipype_smooth.nii.gz', 
          display_mode='ortho', dim=-1, draw_cross=False, annotate=False);
![image](https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.vpb9jcncd7k.webp)

## Nodes and Workflows

接口是Nipype的核心，在使用不同包的接口或者同一个包的不同接口时，需要把接口放在一个Node中，并且多个Node构成一个workflow。

在Nipype中，一个节点是执行某个函数的物体，这个函数可以是Nipype中的任意接口函数或者是user自己创建的函数，又或者是外部的一个脚本中的。每个节点由名称，接口和最少一个输入和输出构成。

一旦使用了多个节点，就可以使用workflow连接每一个节点，并且生成一个有向连接图。

首先创建去头骨节点

    # Create Node
    bet_node = Node(BET(), name='bet')
    # Specify node inputs
    bet_node.inputs.in_file = input_file
    bet_node.inputs.mask = True

    # bet node can be also defined this way:
    #bet_node = Node(BET(in_file=input_file, mask=True), name='bet_node')

mask为True表示生成一个去头骨的掩膜图像。

接下来创建一个光滑节点，使用IsotropicSmooth函数

    smooth_node = Node(IsotropicSmooth(in_file=input_file, fwhm=4), name="smooth")

接下来使用掩膜函数把光滑后的图像掩膜。

    mask_node = Node(ApplyMask(), name="mask")

使用help指令后可以看到，ApplyMask（）函数有两个必须传入的参数。

* `mask_file` :掩膜图像位置

* `in_file` ：被掩膜的图像


所有节点都创建完毕，接下来创建一个workflow把他们连接起来。首先连接`bet_node`的输出和`mask_node`的输入

    # Initiation of a workflow
    wf = Workflow(name="smoothflow", base_dir="/output/working_dir")

    wf.connect(bet_node, "mask_file", mask_node, "mask_file")

接着连接`smooth_node`的out_file和`mask_node`的in_file
把这个workflow可视化出来

    wf.connect(smooth_node,"out_file",mask_node, "in_file")


    wf.write_graph("workflow_graph.dot")
    Image(filename="/output/working_dir/smoothflow/workflow_graph.png")

![image](https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.73aqturs75o0.webp)

或者画出细节图

    wf.write_graph(graph2use='flat')
    
    Image(filename="/output/working_dir/smoothflow/graph_detailed.png")

![image](https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.21b5aym2r528.webp)

接下来开始运行workflow

    res = wf.run()

列出结果

    list(res.nodes)[0].result.outputs


![1690184817148](https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/1690184817148.1ju26tsxsr4w.webp)

查看生成的文件夹

    ! tree -L 3 /output/working_dir/smoothflow/

也可以将生成图片画出

    import numpy as np
    import nibabel as nb
    #import matplotlib.pyplot as plt

    # Let's create a short helper function to plot 3D NIfTI images
    def plot_slice(fname):

        # 加载图像
        img = nb.load(fname)    
        data = img.get_data()

        # 因为是3D图像，所以选择Z轴中间切开
        cut = int(data.shape[-1]/2) + 10   

        # Plot the data
        plt.imshow(np.rot90(data[..., cut]), cmap="gray")
        plt.gca().set_axis_off()  # 关闭坐标轴

    f = plt.figure(figsize=(12, 4))
    for i, img in enumerate(["/data/ds000114/sub-01/ses-test/anat/sub-01_ses-test_T1w.nii.gz",
                            "/output/working_dir/smoothflow/smooth/sub-01_ses-test_T1w_smooth.nii.gz",
                            "/output/working_dir/smoothflow/bet/sub-01_ses-test_T1w_brain_mask.nii.gz",
                            "/output/working_dir/smoothflow/mask/sub-01_ses-test_T1w_smooth_masked.nii.gz"]):
        f.add_subplot(1, 4, i + 1)
        plot_slice(img)


![image](https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.196qnmp51nwg.webp)


## Iterables
在做数据处理的时候，经常一个预处理跑多个受试或者做一些组差异比较。避免重复写脚本，Nipype有一个处理插件，叫做iterables

假如我们有一个workflow上面有两个node，node（A）是去头颅函数，node(B)是光滑函数，现在我们很好奇不同大小的高斯核对结果的影响。因此，我们设置FWHM的值为4mm,8mm和16mm。

![image](https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.5yngzb9aors0.webp)

    smooth_node_it = Node(IsotropicSmooth(in_file=input_file), name="smooth")
    smooth_node_it.iterables = ("fwhm", [4, 8, 16])

重新定义bet和mask node

    bet_node_it = Node(BET(in_file=input_file, mask=True), name='bet_node')
    mask_node_it = Node(ApplyMask(), name="mask")

新建一个新的workflow

    wf_it = Workflow(name="smoothflow_it", base_dir="/output/working_dir")
    wf_it.connect(bet_node_it, "mask_file", mask_node_it, "mask_file")
    wf_it.connect(smooth_node_it, "out_file", mask_node_it, "in_file")

运行workflow

    res_it = wf_it.run()

看生成的文件
    ! tree -L 3 /output/working_dir/smoothflow_it/

    /output/working_dir/smoothflow_it/
    ├── bet_node
    │   ├── _0x059f982d380f7943757debfb10a5502d.json
    │   ├── command.txt
    │   ├── _inputs.pklz
    │   ├── _node.pklz
    │   ├── _report
    │   │   └── report.rst
    │   ├── result_bet_node.pklz
    │   └── sub-01_ses-test_T1w_brain_mask.nii.gz
    ├── d3.js
    ├── _fwhm_16
    │   ├── mask
    │   │   ├── _0xd72ea2e7b364b1159092a12f3d3ca28c.json
    │   │   ├── command.txt
    │   │   ├── _inputs.pklz
    │   │   ├── _node.pklz
    │   │   ├── _report
    │   │   ├── result_mask.pklz
    │   │   └── sub-01_ses-test_T1w_smooth_masked.nii.gz
    │   └── smooth
    │       ├── _0xc478d2c0a35763bb8cc0ba0be7c7c4a3.json
    │       ├── command.txt
    │       ├── _inputs.pklz
    │       ├── _node.pklz
    │       ├── _report
    │       ├── result_smooth.pklz
    │       └── sub-01_ses-test_T1w_smooth.nii.gz
    ├── _fwhm_4
    │   ├── mask
    │   │   ├── _0x020ff51c3dd2cc1c441bbc71b6bb82fd.json
    │   │   ├── command.txt
    │   │   ├── _inputs.pklz
    │   │   ├── _node.pklz
    │   │   ├── _report
    │   │   ├── result_mask.pklz
    │   │   └── sub-01_ses-test_T1w_smooth_masked.nii.gz
    │   └── smooth
    │       ├── _0x7284dba78093a51ae87024eac1bec00f.json
    │       ├── command.txt
    │       ├── _inputs.pklz
    │       ├── _node.pklz
    │       ├── _report
    │       ├── result_smooth.pklz
    │       └── sub-01_ses-test_T1w_smooth.nii.gz
    ├── _fwhm_8
    │   ├── mask
    │   │   ├── _0x3ac21b3bbb4f9177e3221ac52b59a349.json
    │   │   ├── command.txt
    │   │   ├── _inputs.pklz
    │   │   ├── _node.pklz
    │   │   ├── _report
    │   │   ├── result_mask.pklz
    │   │   └── sub-01_ses-test_T1w_smooth_masked.nii.gz
    │   └── smooth
    │       ├── _0x8a60534f7916842abe5b185fdf7996d9.json
    │       ├── command.txt
    │       ├── _inputs.pklz
    │       ├── _node.pklz
    │       ├── _report
    │       ├── result_smooth.pklz
    │       └── sub-01_ses-test_T1w_smooth.nii.gz
    ├── graph1.json
    ├── graph.json
    └── index.html

    17 directories, 47 files


## Mapnode

如果想使输入为多个输入参数的节点的输出都为下一个节点的输入，那么就需要使用**MapNode**。MapNode与普通的Node很像，它的输入可以是一个列表，最终也生成一个输出列表。

比如你有许多文件，他们都会经过相同的node处理和不同的Node，这时MapNode可以发挥作用。
![image](https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.51d5xj2wdbc0.webp)

比如上图，A节点处理后得到一个列表的输出，而这些输出都会经过B节点处理，最后把B得到的结果送入C节点。

简单例子：

    def square_func(x):
    return x ** 2

    # 这里使用nipype的Function接口，把用户自己创建的函数作为一个接口
    square = Function(input_names=["x"], output_names=["f_x"], function=square_func)

如果把输入的值设置为一个列表

    square_node = Node(square, name="square")
    square_node.inputs.x = [2, 4]
    res = square_node.run()
    res.outputs

输出会报错，因为`square_func`不能接收一个列表。

这时我们使用MapNode即可解决！

    square_mapnode = MapNode(square, name="square", iterfield=["x"])
    square_mapnode.inputs.x = [2, 4]
    res = square_mapnode.run()
    res.outputs
![image](https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.3wcljq4l6ya0.webp)

## 参考

nipype官方教程 [Nipype Quickstart](https://miykael.github.io/nipype_tutorial/notebooks/introduction_quickstart.html)