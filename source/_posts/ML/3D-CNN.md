---
title: 3D-CNN学习
date: 2022-9-21 20:42
categories: 
  - ML
tags:
  - Deep Learning
  - CV
  - CNN
---



# 3D-CNN学习

## 2D卷积

传统的2D的 **卷积神经网络(CNN)** 使用一个卷积核在2D空间上滑动卷积操作

![2D单通道卷积](https://raw.githubusercontent.com/Xav1erW/blog-imgs/master/202209212047851.gif)

如果是多通道卷积，那么卷积核通道和图片通道数是一致的，并且 **不同通道的卷积核参数不同**

![2D多通道卷积](https://raw.githubusercontent.com/Xav1erW/blog-imgs/master/202209212050198.gif)

通过2D的卷积，我们可以感受出，卷积操作实际上是一个卷积核在一个空间中滑动的，滑动重合的部分进行乘weight并加上bias的操作

2D的卷积是在平面空间进行上述操作的，那么，能否将上述操作推广到更广的空间中？

## 3D卷积

答案是可以的。

![3D-CNN](https://raw.githubusercontent.com/Xav1erW/blog-imgs/master/202209212108630.png)

当我们有很多层的图片 （比如视频中的连续帧）时，将这些二维数据叠在一起，形成一个三维结构，通过在三维空间移动这样的三维卷积核并进行卷积计算，得到卷积的结果，如上图。

![3D-CNN-animation](https://raw.githubusercontent.com/Xav1erW/blog-imgs/master/202209212130189.gif)

如果传入的为拥有多通道的图片序列（如RGB图片），那么上述卷积核每个位置还要在通道方向上为每个通道拆分出一层，可以理解为 **分别对每个通道进行三维卷积操作** 

### 与二维卷积的区别

1. 首先卷积核为3维的。虽然多通道的二维卷积中卷积核也可以看成有多个第三维度的层，但这些层在卷积操作中互相独立不产生影响；而三维卷积中卷积核拥有的深度(depth)维度之间会在操作中有加和操作，最终转化为输出结果的一个数
2. 滑动空间是三维空间。3D卷积会在深度方向上滑动并卷积，而2D卷积不存在深度方向。

**参考：**

>  [2D-CNN和3D-CNN计算 - 掘金 (juejin.cn)](https://juejin.cn/post/6844903957307850759)
>  [3D Convolutions : Understanding + Use Case | Kaggle](https://www.kaggle.com/code/shivamb/3d-convolutions-understanding-use-case/notebook)
>
>  [1D & 3D Convolutions explained with… MS Excel! | by Thom Lane | Apache MXNet | Medium](https://medium.com/apache-mxnet/1d-3d-convolutions-explained-with-ms-excel-5f88c0f35941)
