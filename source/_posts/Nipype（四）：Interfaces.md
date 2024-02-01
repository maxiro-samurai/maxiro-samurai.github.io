---
title: Nipype（四）：Interfaces
tags:
  - 神经影像
categories:
  - python
date: 2023-08-03 15:45:29
---

# Interfaces
在之前已经学习过Interfaces的基本概念和操作，这里简单介绍一下。Interfaces就是其他软件包的python函数接口，想要使用其他软件的（例如FSL,SPM或者FreeSurfer）函数，就需要此函数的python接口和安装相应的软件包。

## BET函数原始使用方法
先看一下我们需要去头骨的原始图像。

    from nilearn.plotting import plot_anat
    %matplotlib inline
    plot_anat('/data/ds000114/sub-01/ses-test/anat/sub-01_ses-test_T1w.nii.gz', title='original',
          display_mode='ortho', dim=-1, draw_cross=False, annotate=False);

![image](https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.4xdlr3xmwg00.webp)

使用BET函数命令行形式（即在FSL中原始使用方法）

    bet <input> <output>

在ipython中则需要在前面加`%%bash`

    %%bash

    FILENAME=/data/ds000114/sub-01/ses-test/anat/sub-01_ses-test_T1w

    bet ${FILENAME}.nii.gz /output/sub-01_ses-test_T1w_bet.nii.gz

使用查看帮助信息

    !bet -h

    Usage:    bet <input> <output> [options]

    Main bet2 options:
    -o          generate brain surface outline overlaid onto original image
    -m          generate binary brain mask
    -s          generate approximate skull image
    -n          don't generate segmented brain image output
    -f <f>      fractional intensity threshold (0->1); default=0.5; smaller values give larger brain outline estimates
    -g <g>      vertical gradient in fractional intensity threshold (-1->1); default=0; positive values give larger brain outline at bottom, smaller at top
    -r <r>      head radius (mm not voxels); initial surface sphere is set to half of this
    -c <x y z>  centre-of-gravity (voxels not mm) of initial mesh surface.
    -t          apply thresholding to segmented brain image and mask
    -e          generates brain surface as mesh in .vtk format

    Variations on default bet2 functionality (mutually exclusive options):
    (default)   just run bet2
    -R          robust brain centre estimation (iterates BET several times)
    -S          eye & optic nerve cleanup (can be useful in SIENA)
    -B          bias field & neck cleanup (can be useful in SIENA)
    -Z          improve BET if FOV is very small in Z (by temporarily padding end slices)
    -F          apply to 4D FMRI data (uses -f 0.3 and dilates brain mask slightly)
    -A          run bet2 and then betsurf to get additional skull and scalp surfaces (includes registrations)
    -A2 <T2>    as with -A, when also feeding in non-brain-extracted T2 (includes registrations)

    Miscellaneous options:
    -v          verbose (switch on diagnostic messages)
    -h          display this help, then exits
    -d          debug (don't delete temporary intermediate images)

使用命令行生成二进制掩膜文件。

    %%bash

    FILENAME=/data/ds000114/sub-01/ses-test/anat/sub-01_ses-test_T1w

    bet ${FILENAME}.nii.gz /output/sub-01_ses-test_T1w_bet.nii.gz -m


由此我们可知道python接口的函数实际上也是在命令行中输入需要的操作，不过使用的是python脚本形式。

## BET函数接口使用

在之前有，这里简要介绍。

    # import BET函数
    from nipype.interfaces.fsl import BET
    skullstrip = BET() # 对象实例化
    skullstrip.inputs.in_file = "/data/ds000114/sub-01/ses-test/anat/ sub-01_ses-test_T1w.nii.gz" # 输入文件路径
    skullstrip.inputs.out_file = "/output/T1w_nipype_bet.nii.gz" # 输出文件路径
    res = skullstrip.run() # 运行

![image](https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.vyddlx4ra8g.webp)

我们来看一下输出的命令行

    print(skullstrip.cmdline)

    bet /data/ds000114/sub-01/ses-test/anat/sub-01_ses-test_T1w.nii.gz /output/T1w_nipype_bet.nii.gz

与我们正常使用时的一样。


## Interface errors

使用`run`方法运行接口时，对于FSL、Freesurfer和其他程序，会使系统输入上述命令行命令。对于MATLAB的程序如SPM，它会生成一个.m文件，然后在matlab环境下运行这个.m文件。

如果我们不给函数接口一个必要的输入参数它就会报错

    skullstrip2 = BET()
    try:
        skullstrip2.run()
    except(ValueError) as err:
        print("ValueError:", err)
    else:
        raise

    ValueError: BET requires a value for input 'in_file'. For a list of required inputs, see BET.help()

BET函数没有输入文件的路径

又或者输入类型搞错，比如BET（）函数想要生成掩膜文件，你却把淹没文件的名字输入上去了 XD。

    try:
    skullstrip.inputs.mask = "mask_file.nii"
    except(Exception) as err:
        if "TraitError" in str(err.__class__):
            print("TraitError:", err)
        else:
            raise
    else:
        raise
    TraitError: The 'mask' trait of a BETInputSpec instance must be a boolean, but a value of 'mask_file.nii' <class 'str'> was specified.

在报错的时候根据报错信息修改程序


## 参考
nipype_tutorial [Interface](https://miykael.github.io/nipype_tutorial/notebooks/basic_interfaces.html)