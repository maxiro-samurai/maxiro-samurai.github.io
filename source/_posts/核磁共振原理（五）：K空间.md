---
title: 核磁共振原理（五）：K空间
tags:
  - MRI
categories:
  - 影像基础
mathjax: true
date: 2023-06-01 16:07:54
---


# K空间和傅里叶变换
在之前的学习中一直参考的是《磁共振成像技术指南》这本书，但是看书还是无法理解K空间和相位编码部分的知识。在网上找了一些资料看了一下，决定看看这部分数学推导，这样应该可以帮助理解。

## K空间数学原理
在主磁场的+z方向上的梯度磁场激发下，假设某一个平面h(x,y)的进动频率为f<sub>0</sub>，那么理论证明，该切面产生的FID信号可以表示为

$$s(t)=\int\int h(x,y)e^{-j2\pi f_0t}dxdy$$

上述公式推导过程非常复杂，我并没有看太懂 贴个连接 [MRI原理-信号](https://zhuanlan.zhihu.com/p/137255997) 有解释

现在要通过S(t)把h(x,y)表示出来，h(x,y)表示二维的灰度值图像，那么该图像的频域表达式可以写为

$$H(u,v)=\int \int h(x,y)e^{-j2\pi ux}e^{-j2\pi vy}dxdy$$

我们如果能求出H（u,v），那么就能通过傅里叶变换求出h(x,y)。


于是，为了求解H(x,y)，人们引出了x和y方向上的梯度场，以此来标明连续空间中，不同位置的点在位置特定的场强（B0 + zGz和该点的Gx和Gy的矢量和）下对FID信号做出的贡献。

则在原先f<sub>0</sub>的场强变为（xGx,yGy,B0+zGz）的一个矢量，f<sub>0</sub>就变成与x和y相关的函数

$$ s(t) = e^{-2\pi \gamma (B_0+zG_z)t}\int \int h(x,y)e^{-j2\pi \gamma (xG_xt_x+yG_yt_y)}dxdy$$

上式对比二维傅里叶变换可得

$$s(t) =  e^{-2\pi \gamma (B_0+zG_z)t}H(u,v)$$

其中 $u=\gamma G_xt_x$, $v=\gamma G_yt_y$

所以得到的S（t）经过解调制后，就能得到H（u,v），然后再进行傅里叶变换就可以得到原始图像信号。
由有限个H（u,v）填满的矩阵，就叫做K-space。而为了填满K-space，不断的改变梯度场时间t， 和梯度场大小的操作， 就叫做频率编码和相位编码。

<br/><br>
<div>			<!--块级封装-->
    <center>	<!--将图片和文字居中-->
    <img src="https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.46388k41mko0.webp"
        alt="寄"
        style="zoom:这里写图片的缩放百分比"/>
    <br>		<!--换行-->
    GX与GY在K空间的效应	<!--标题-->
    </center>
</div>
<br><br> 

## 频率编码
通过上图可以发现，当G<sub>y</sub>为零时，G<sub>x</sub>所持续的是填T<sub>x</sub>共同决定了K空间的K<sub>x</sub>轴，相反，G<sub>y</sub>和T<sub>y</sub>共同决定了K<sub>y</sub>轴

习惯上我们先沿着X轴方向采集信号，其中采集信号的方式就是采用脉冲时间序列。
<br/><br>
<div>			<!--块级封装-->
    <center>	<!--将图片和文字居中-->
    <img src="https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.kusysrz6d5s.webp"
        alt="寄"
        style="zoom:这里写图片的缩放百分比"/>
    <br>		<!--换行-->
    在X方向上采样	<!--标题-->
    </center>
</div>
<br><br> 
“slice refocusing gradient" 这里的作用是当RF脉冲序列的频宽过大，与之发生共振的组织也会更多，共振频带过大，这导致切片出来的信号会出现小幅度的相位差，从而使得采集图像模糊。因此这里会加上一个slice refocusing gradient，使得RF频带中的组织都在一个比较小范围差的频率下发生共振。

<br>

我的理解是由于进动频率由磁场强度决定，所以原来的激发范围很大，加上相反的磁场强度后，位于前面的质子频率增大，位于后面的质子频率减小，最后得到了一个相对小的激发频率带宽。
<br/><br>
<div>			<!--块级封装-->
    <center>	<!--将图片和文字居中-->
    <img src="https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.vy6abjp0ysg.webp"
        alt="寄"
        style="zoom:这里写图片的缩放百分比"/>
    <br>		<!--换行-->
    大概理解	<!--标题-->
    </center>
</div>
<br><br> 

这里G<sub>x</sub>不变，在T<sub>x</sub>采样表示，在X轴上朝右移动。

每次采样中，同一个x位置下的组织产生的是相同的共振频率。这也就是说，Kspace中，垂直于x轴线的每一条采样轨迹(trajectory), 都具有相同频率分布。换句话说，在kspace这一二维坐标系下，同一个横轴坐标表示具有同一个频率。这种采样方法形成的轨迹被叫做笛卡尔采样轨迹(Cartisan Trajectory)。
而这种在x轴方向上固定梯度磁场大小，只改变磁场时间的采样方式，就叫做频率编码。


为了能够既采样负x轴方向，又采集x的正轴方向，人们选择先从x轴的负方向出发一直到最左端，然后再掉头往x的正方向去。因此需要先加上一个负梯度，然后才是正梯度。

<br/><br>
<div>			<!--块级封装-->
    <center>	<!--将图片和文字居中-->
    <img src="https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.42ptm8do39q0.webp"
        alt="寄"
        style="zoom:这里写图片的缩放百分比"/>
    <br>		<!--换行-->
    梯度先负再正	<!--标题-->
    <img src="https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.2q9mq7svhas0.webp"
        alt="寄"
        style="zoom:这里写图片的缩放百分比"/>
    K空间内填充顺序
    </center>
</div>
<br><br>

负梯度施加一个周期后，施加正梯度两个周期正好把X轴的一行采集完毕。这种梯度场产生的回波信号叫做梯度回波信号（Gradient Echo, GE）


## 相位编码

相对的在y轴上实行相位编码。而实际中X轴做频率编码还是相位编码并不是固定的，这两个轴采样方法可以反过来。

<br/><br>
<div>			<!--块级封装-->
    <center>	<!--将图片和文字居中-->
    <img src="https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.4sj4j9tl0fm0.webp"
        alt="寄"
        style="zoom:这里写图片的缩放百分比"/>
    <br>		<!--换行-->
    相位编码	<!--标题-->
    </center>
</div>
<br><br> 
如果固定x轴的梯度Gx和磁场时间tx，即使得信号只沿着K-space的y轴发生平移，然后不停的改变Gy的大小，这造成了y方向上的每一个组织点都有不同的磁场大小，由此会导致不同的拉莫尔频率，而不同的拉莫尔频率又会导致组织点们out of phase，与之前学习的相位编码相同。在这里不同的G<sub>y</sub>代表了不同T<sub>y</sub>时刻Y轴的等间隔采样。这种采样方式称为相位编码。
<br/><br>
<div>			<!--块级封装-->
    <center>	<!--将图片和文字居中-->
    <img src="https://cdn.staticaly.com/gh/maxiro-samurai/image-bed@main/image/image.738rxlxqfy80.webp"
        alt="寄"
        style="zoom:这里写图片的缩放百分比"/>
    <br>		<!--换行-->
    填充K空间和傅里叶反变换后成像	<!--标题-->
    </center>
</div>
<br><br> 


# 参考
《磁共振成像技术指南》—— 杨正汉

知乎：[MRI—从产生信号到生成图像（二)](https://zhuanlan.zhihu.com/p/344864875)   作者：嘭噗啪嚓吧

[MRI原理-信号](https://zhuanlan.zhihu.com/p/137255997)   