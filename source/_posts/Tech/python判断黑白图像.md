---
title: python判断黑白图像
date: 2022-07-03 12:15:04
categories: 
  - Tech
  - snippet
tags:
  - 图像处理
  - python
  - snippet
---

# python判断黑白图像

```python
def is_grayscale(img:Image):
    im = img.convert("RGB")
    stat = ImageStat.Stat(im)

    if sum(stat.sum)/3 == stat.sum[0]:
        return True
    else:
        return False
```

上述代码通过计算红、绿、蓝三种颜色的和，如果三个和的平均值和任意一个相等(代码中取的是第0个)，那么认为是灰度图（因为灰度图r=g=b）



> [rgb - Python Fastest way to check if image is greyscale or color? - Stack Overflow](https://stackoverflow.com/questions/60783350/python-fastest-way-to-check-if-image-is-greyscale-or-color)
