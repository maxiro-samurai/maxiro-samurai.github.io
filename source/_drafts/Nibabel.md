---
title: Nibabel
tags:
---
# Nibabel库

python用于读取Nifti文件的包

## Nibabel images

nibabel图像对象包含3部件

* N维图像信息即图像数组
* [4,4]数组存放仿射矩阵（把体素坐标转化到RAS世界坐标下）
* 头文件保存一些其他信息

## 图像对象读取

```
    import nibabel as nib
    img = nib.load(example_file) #文件绝对路径
```

`img`是`nibabel.nifti1.Nifti1Image`的实例

    img.dataobj
    <nibabel.arrayproxy.ArrayProxy object at ...>

`img.dataobj`是一个指针指向图像数组

`affine`是仿射矩阵，仿射矩阵作用是把图像体素坐标转化到RAS+真实世界的坐标中。原理是利用矩阵乘法对坐标进行线性变换（放大、缩小、旋转、平移）从而转化到现实坐标系统。[Coordinate systems and affines](https://nipy.org/nibabel/coordinate_systems.html)