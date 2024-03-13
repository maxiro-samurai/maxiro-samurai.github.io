---
title: 远程保存hexo博客仓库
tags: 杂记
date: 2024-03-13 22:08:41
---


# 问题来源

博主之前用的是笔记本，最早的博客都存在笔记本上，读研之后电脑设备增多，工位一个，宿舍一个。如果用之前的方法，每个电脑上都保存一个博客数据，更新起来很不方便。于是我想到这个问题正好可以用Github解决（Github使用不熟练），所以在网上找了一个方法，本篇博客为记录并且复刻方法。

参考: [hexo个人博客：换了电脑怎么办](https://blog.csdn.net/heimu24/article/details/81210640)

# 解决方案

首先需要知道，hexo生成的静态文件会放在public/文件夹中，部署就是把public文件夹中的内容上传到git仓库中。

* 方案一：在github仓库上新建一个仓库，然后把blog文件夹上传进行备份。

* 方案二: 在博客仓库创建一个新的分支，使用分支来管理。

## 步骤
本文采用方案二

### 1.拷贝远程仓库
进入D盘，右键点击Git bash here

``` 
git clone git@github.com:maxiro-samurai/maxiro-samurai.github.io.git
```
把项目文件夹拷贝到本地

### 2.删除文件夹内容
进入`maxiro-samurai.github.io.git`项目文件夹中，删除其中的内容，把blog文件夹（即备份的blog）下全部复制到其中

### 3. .gitignore文件
打开.gitignore文件，这个文件的作用是指定哪些文件上传的时候可以忽略，因为blog/的文件并不全部都需要

```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/

```
意思就是上传的时候忽略以上内容


### 4.创建一个hexo（任意名字）的分支并切换到这个分支

    git checkout -b hexo 

把所有文件添加到暂存区

    git add --all

提交到本地版本库

    git commit -m ""

推送hexo分支的文件到github仓库

    git push --set-upstream origin hexo

### 5. 结束


![image](https://github.com/maxiro-samurai/picx-images-hosting/raw/master/image.1zi0g7771p.png)

效果


![image](https://github.com/maxiro-samurai/picx-images-hosting/raw/master/image.7p3crs3ype.webp)

这个就是最后需要备份的文件夹


### 6.更新博客


* 生成草稿
```
hexo new draft 远程保存hexo博客仓库
```
![image](https://github.com/maxiro-samurai/picx-images-hosting/raw/master/image.67x7q1a3my.webp)

* 发布博客

        hexo publish 远程保存hexo博客仓库
        hexo g 
        hexo d

* 更新备份

        git add.

        git commit -m"注释"

        git push origin hexo



至此大功告成，在新电脑上只需要从远程仓库clone到本地，然后按照上述步骤进行更新博客即可

