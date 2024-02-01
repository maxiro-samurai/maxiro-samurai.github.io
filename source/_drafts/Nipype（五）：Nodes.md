---
title: Nipype（五）：Nodes
tags:
  - 神经影像
categories:
  - python
---
# Node
在整个工作流程中，每一个操作的**Interface**放入一个**Node**中，把不同的**Node**连接起来就构成了一个**Workflow**.

## Simple example

一个节点由以下元素构成：

    nodename = Nodetype(interface_function(), name='labelname')

* `nodename`: 变量名称
* `Nodetype`: 节点类型，一共有3个类型Node、MapNode、JoinNode。
* `interface_function`: 接口方程或者用户自己定义的方程
* `labelname`：节点的标签名称（也是工作文件夹的名称） 

