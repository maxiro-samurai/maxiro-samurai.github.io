---
title: M-watch踩坑记录
date: 2022-04-06 11:26:50
---


# M-watch项目记录

* 无线电源部分  

    BQ51013b  

    替换参考设计的时候要思考，不能用简化器件替代


    <br/><br>
    <div>			<!--块级封装-->
        <center>	<!--将图片和文字居中-->
        <img src="https://cdn.jsdelivr.net/gh/maxiro-samurai/image-bed@main/image/47aeb1612ea6b0c9c7106072513b530.6fn4o2h9h3o0.webp"
            alt="寄"
            style="zoom:这里写图片的缩放百分比"/>
        <br>		<!--换行-->
        参考设计	<!--标题-->
        </center>
    </div>
    <br><br>  

    仅用一个PMOS管代替肯定不行，因为此电路电源双向导通的，OUT端电压先产生，会直接影响AD端电压，使无线充电关闭。  


 

    