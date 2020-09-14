---
title: FCOS源码分析及使用
date: 2019-4-18 19:13:39
categories: project
tags:
    - object detection
---
[mmdetection][1]是一款优秀的基于PyTorch的开源目标检测系统，由香港中文大学(CUHK)多媒体实验室(mmlab)开发。基本上支持所有当前SOTA二阶段的目标检测算法，比如faster rcnn，mask rcnn，r-fcn，cascade rcnn，此外还支持了SSD和RetinaNet等一阶段的目标检测算法。花了一天的时间大致地看了下框架的相关源代码，以下是部分整理纪录。
![](/img/coco_test_12510.jpg)
<!-- more -->
### 安装
项目地址：[open-mmlab/mmdetection][2]。安装：[mmdetection/INSTALL.md][3]。
当前该项目已经迁移到pytorch1.0版本，如果需要使用pytorch0.4可以使用如下命令进行切换：



**错误解决方案**：
我在成功配置好FCOS的环境之后，运行代码：
```
CUDA_VISIBLE_DEVICES=1 python tools/test_net.py --config-file configs/fcos/fcos_R_50_FPN_1x.yaml MODEL.WEIGHT models/FCOS_R_50_FPN_1x.pth TEST.IMS_PER_BATCH 8
```
出现了如下错误：
```
ERROR: Unexpected bus error encountered in worker. This might be caused by insufficient shared memory (shm).
```
解决方法：将FCOS/maskrcnn_benchmark/config/defaults.py中的_C.DATALOADER.NUM_WORKERS设置为0。
最后的测试结果如下：
```
Average Precision  (AP) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.366
 Average Precision  (AP) @[ IoU=0.50      | area=   all | maxDets=100 ] = 0.561
 Average Precision  (AP) @[ IoU=0.75      | area=   all | maxDets=100 ] = 0.389
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.209
 Average Precision  (AP) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.403
 Average Precision  (AP) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.473
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=  1 ] = 0.316
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets= 10 ] = 0.501
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=   all | maxDets=100 ] = 0.525
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= small | maxDets=100 ] = 0.333
 Average Recall     (AR) @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] = 0.572
 Average Recall     (AR) @[ IoU=0.50:0.95 | area= large | maxDets=100 ] = 0.673

```
