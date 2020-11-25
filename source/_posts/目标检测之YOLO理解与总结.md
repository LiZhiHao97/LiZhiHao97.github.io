---
title: 目标检测之YOLO理解与总结
date: 2020-11-25 13:31:04
tags:
  - 深度学习
  - 目标检测
  - YOLO
---

# 1.背景

- 我们知道，目标检测通常分为两个步骤： **（1）生成bounding box（边界框）（2）对物体分类**。因此目标检测算法也因此分为**one-stage**与**two-stage**两个算法系列。
- 顾名思义，**two-stage**算法就是把两个步骤分别进行，比如**R-CNN**方法使用`region proposal`来生成整张图像中可能包含待检测物体的`potential bounding boxes`，然后用分类器来评估这些`boxes`，接着通过`post-processing`来改善`bounding boxes`，消除重复的检测目标，并基于整个场景中的其他物体重新对`boxes`进行打分。整个流程执行下来很慢，而且因为这些环节都是分开训练的，检测性能很难进行优化。
- 作者提出的**YOLO（you only look once）**就是**one-stage**算法系列的代表。它将目标检测任务当做回归问题`（regression problem）`来处理，直接通过整张图片的所有像素得到 的坐标、box中包含物体的置信度和`class probabilities`。顾名思义，通过**YOLO**，每张图像只需要看一眼就能得出图像中都有哪些物体和这些物体的位置。

# 2.内容介绍

本篇博客记录我对YOLO的学习，我会分别对YOLOV1，YOLOV2，YOLOV3的内容进行总结，并进行比较。

# 3.YOLO V1

## 3.1 流程

1. 将一幅图像分成**S×S**个网格（grid cell），如果某个`object`的中心落在这个网格中，则这个网格就负责预测这个`object`
2. 每个网格要预测**B**个`bounding box`，每个`bounding box`除了要预测位置之外，还要附带预测一个`confidence(置信度)`。每个网格还要预测**C**个类别的分数

![](/image/目标检测之YOLO理解与总结/1.png)

比如将上图分为了7×7个网格，要识别的目标狗的中心点落在第5行，第2列的网格点，那么这个网格点就负责预测狗这个object

![](/image/目标检测之YOLO理解与总结/2.png)

> For evaluating YOLO on PASACL VOC, we use S = 7, B = 2, PASCAL VOC has 20 labelled classes so C = 20. Our final prediction is a 7 × 7 × 30 tensor

以上为摘自原论文，在`PASCAL VOC`数据集上，我们通过YOLO，最终获得了**7 × 7 × 30**的特征矩阵，由上图可知，沿着深度方向我们发现有**30**个数值，因为**B = 2**，所以有2个`bounding box`，每个`bounding box`有**4+1**个数值，分别为目标的`坐标信息(中心点位置和宽高)`与`confidence`，最后**20**个为PASCAL VOC **20**个类别的分数。所以总共是**30**

## 3.2 网络结构

YOLO将图像resize到448×448作为神经网络的输入 。

YOLO检测网络包括**24个卷积层和2个全连接层**，YOLO网络借鉴了**GoogLeNet**分类网络结构。不同的是，YOLO未使用inception module，而是使用1x1卷积层（此处1x1卷积层的存在是为了跨通道信息整合）+3x3卷积层简单替代。、

![](/image/目标检测之YOLO理解与总结/3.png)

## 3.3 损失函数

YOLO V1的损失函数分为三个部分

![](/image/目标检测之YOLO理解与总结/4.png)

计算损失过程中，用的都是误差平方和，但是对宽高处理，是先对其开根号处理，为什么这么做呢，看到一个很棒的解释。

![](/image/目标检测之YOLO理解与总结/5.png)

从上图右侧所示，当预测边界框相对于真实边界框便宜相同的高度和宽度时，小目标改变的IoU与大目标不同。画出y - x和$ \sqrt{y} $ - $ \sqrt{x} $图，如左侧所示，我们会发现,y与x的差值一样时,小目标在纵坐标上的差值大于大目标

## 3.4 YOLO V1的不足之处

- YOLO V1对**小目标**的预测效果不理想,因为在YOLO V1的每个grid cell只预测2个`bounding box`,而且这两个`bounding box`属于同个类别
- 当检测目标出现了新的尺寸时,检测效果会变差
- 主要的错误原因,都来自于定位不准确,因为不像**Faster RCNN**和**SSD**使用了**anchor box**的回归参数来调整位置


# 4.YOLO V2

## 4.1 对比YOLO V1

作者在YOLO v1的基础上，借鉴了很多**trick**，比如`Batch Normalization`、`High Resolution Classifier`等，提出YOLO v2，让预测变得更**准确（Better）**，**更快速（Faster）**，下图为性能对比。

![](/image/目标检测之YOLO理解与总结/6.png)
![](/image/目标检测之YOLO理解与总结/123.png)

## 4.2 YOLO V2中的各种尝试

### 1. Batch Normalization（批量归一化）

- **批量归一化**有助于解决反向传播过程中出现的**梯度消失**和**梯度爆炸**问题，降低对一些超参数的敏感性，并且起到一定的**正则化**效果（YOLO2不再使用dropout），从而能够获得更好的收敛速度和收敛效果，加了BN层后mAP提高了**2%**

### 2.High Resolution Classifier（使用高分辨率图像微调分类模型）

- 目前先进的目标检测方法中，基本上都会先在`ImageNet`上进行预训练，而`ImageNet`的输入采用的是**224×224**，导致分辨率不够高，给检测带来困难。**YOLO v1**网络把分辨率直接提升到了**448×448**，但是直接切换分辨率，检测模型可能难以快速适应高分辨率，这也意味之原有的网络模型必须进行**某种调整**以适应新的分辨率输入。
- 所以**YOLO v2**增加了在`ImageNet`数据集上使用**448×448**输入来**finetune**分类网络这一中间过程（10 epochs），这可以使得模型在检测数据集（eg:COCO）上**finetune**之前已经**适应**高分辨率输入。使用高分辨率分类器后，YOLOv2的mAP提升了约**4%**。

### 3.Convolutional With Anchor Boxes（使用先验框Anchor Box替换全连接层）

在之前的**YOLO v1**中，直接采用**全连接层**来预测边界框，效果不好(之前分析中的定位效果较差)。作者借鉴了**Faster R-CNN**中使用**RPN**预测**偏移量offsets**和**置信度confidences**的思想，预测**bounding box**与**Anchor**框的**偏移（offset）**而不是直接预测**bounding box**的坐标。

1. 首先作者**去除了一个池化层**，来使得输出的卷积特征图有**更高的分辨率**。
2. 其次作者**缩减了网络**，把原本网络的输入**448×448** 缩减成**416×416**。因为**YOLO v2**模型**下采样**的总步长为**32**，对于**416×416	**大小的图片，最终得到的特征图大小为**13×13**，维度是**奇**数，这样特征图恰好只有一个中心位置。**作者发现对于一些大物体，它们中心点往往落入图片中心位置，此时使用特征图的一个中心点而不是四个中心点去预测这些物体的边界框相对容易些**。所以在YOLOv2设计中要保证输入对应的最终的特征图有奇数个位置

- 使用**anchor boxes**之后，mAP下降了**0.3**，但召回率提高了**7%**。这是因为**YOLOv1**只能预测**98**个边界框**(7×7×2)**，而**YOLO v2**使用**anchor boxes**之后可以预测**上千个边界框（13×13×num_anchor）**,召回率的提升使得模型拥有进一步的改进空间。

### 4.Dimension Clusters(使用聚类算法获取anchor box)

- 之前**anchor boxes**都是**手工设定**的(如**Faster RCNN**,作者没有给出具体获取anchor boxes的方法,只是根据经验所得)，网络微调**anchor boxes**到实际位置的难度不小。而**YOLO v2的做法是对训练集中标注的边框进行聚类分析，以自动找到更好的尺寸。**

- 聚类算法最重要的是选择如何计算两个边框之间的“距离”，对于常用的欧式距离，大边框会产生更大的误差，但我们关心的是**anchor boxes**和**ground truth**的**IOU**。所以，**YOLO2在聚类时巧妙地采用以下公式来计算两个边框之间的距离：**

  **d(box, centroid) = 1 - IoU(box, centroid)**
  
- **centroid是聚类时被选作中心的边框，box就是其它anchor boxes。IOU越大，距离越近。**YOLO2给出的聚类分析结果如下图所示：

  ![](/image/目标检测之YOLO理解与总结/7.png)

- 随着聚类中心数目的增加，我们可以看到平均IOU值是增加的，但是综合考虑模型复杂度和召回率，作者最终选取**5**个聚类中心作为先验框，其相对于图片的大小如右边图所示。

### 5.Direct location prediction(直接位置预测)

- 作者发现使用**anchor boxes**的时候模型**不稳定**，尤其是在早期迭代的时候。而大部分的不稳定现象出现在预测box的**(x, y)**坐标上了，而以往计算的公式如下（**图中的两个减号应是加号，作者写错**）：

    ![](/image/目标检测之YOLO理解与总结/8.png)

- 这个公式没有任何限制，使得无论在什么位置进行预测，任何anchor boxes可以在图像中任意一点结束（这是因为偏移量**tx, ty**没有大小限制，可能会导致出现anchor偏离ground truth很远的情况。正确做法应该是每一个anchor只负责检测周围正负一个单位以内的目标box）。YOLO v2调整了预测公式，将**预测边框的中心约束在网格内**:

	![](/image/目标检测之YOLO理解与总结/9.png)

- 其中，**bx, by, bw, bh**是**预测边框的中心和宽高**。
**Pr(object) * IoU(b, object) = σ(to)** 是**预测边框的置信度**，**YOLO v1**是直接预测置信度的值，这里对预测参数**to**进行σ变换后作为置信度的值。**cx, cy**是**当前网格左上角到图像左上角的距离**，要先将**网格大小归一化，即令一个网格的宽=1，高=1**。$ p^w $, $ p^h $ 是**先验框的宽和高**。 σ是**sigmoid**函数。**tx, ty, tw, th, to**是要学习的参数，分别用于**预测边框的中心和宽高，以及置信度**。

![](/image/目标检测之YOLO理解与总结/10.png)

- 作者使用Dimension Clusters和Direct location prediction这两项anchor boxes改进方法，mAP获得了**5%**的提升。



### 6.Fine-Grained Features（细粒度特征）

- 作者发现**YOLO**使用**13×13**的特征图进行预测**大物体**拥有很好的效果，而相对比较小的物体则效果一般。**YOLO v2**引入一种称为**passthrough**层的方法在特征图中保留一些细节信息。

- **YOLO v2**所利用的**Fine-Grained Features**是**26×26**大小的特征图（最后一个maxpooling层的输入）。**passthrough**层与**ResNet**网络的**identity mappings**类似，**以前面更高分辨率的特征图为输入，然后将其连接到后面的低分辨率特征图上**。前面的特征图维度是后面的特征图的**2**倍，**passthrough**层抽取前面层的每个**2×2**，然后将其转化为channel的维度，对于**26×26×512**的特征图，经**passthrough**层处理之后就变成了 **13×13×25**6的新特征图（特征图大小降低4倍，而channles增加4倍），这样就可以与后面的**13×13×25**6 特征图连接在一起形成**13×13×1280**大小的特征图。
	![](/image/目标检测之YOLO理解与总结/11.png)
	![](/image/目标检测之YOLO理解与总结/12.png)
### 7.Multi-Scale Training（多尺寸训练）

- 因为**Yolo v2**去掉了全连接层，只有卷积层与池化层，所以对于网络的输入大小，并没有限制，整个网络的**降采样倍数为32**，只要输入的特征图尺寸为32的倍数即可。**所以Yolo v2可以使用不同尺寸的输入图片训练，来使模型适应不同分辨率的图片，让模型更鲁棒**。
- **具体来说就是在训练过程中每间隔一定的iterations（论文中是10）之后改变模型的输入图片大小**。由于**YOLOv2**的下采样总步长为**32**，输入图片大小选择一系列为**32倍数**的值：**{320,  352, ......, 608}**，输入图片最小为**320×320**，此时对应的特征图大小为**10×10**（和**Convolutional With Anchor Boxes**中提到要是奇数相悖，但是问题也不是很大），而输入图片最大为**608×608**，对应的特征图大小为**19×19**。在训练过程，每隔**10个iterations**随机选择一种输入图片大小，然后只需要修改对最后检测层的处理就可以重新训练。
- 在**小尺寸**图片检测中，**YOLO v2**效果很好，输入为**228×228**的时候，帧率达到**91FPS**，**mAP几乎和Faster R-CNN的水准相同**。在**大尺寸**图片检测中，**YOLO v2**也达到了先进水平，在VOC2007数据集上**mAP为78.6%**，仍然高于平均水准，下图是**YOLO v2**和其他网络的效果对比：
	![](/image/目标检测之YOLO理解与总结/13.png)

### 8.BackBone: Darknet-19

- YOLOv2采用了一个新的网络，称为**Darknet-19**，包括19个卷积层和5个maxpooling层，在`ImageNet`数据集上，**Darknet-19的top-1准确度为72.9%，top-5准确度为91.2%，但是模型参数相对小一些**。使用Darknet-19之后，YOLOv2的mAP值没有显著提升，但是**计算量却可以减少约33%**。
	![](/image/目标检测之YOLO理解与总结/14.png)

## 4.3 网络结构

![](/image/目标检测之YOLO理解与总结/11.png)

## 4.4 关于网络的训练细节

> We use a weight decay of 0.0005 and momentum of 0.9. We use a weight decay of 0.0005 and momentum of 0.9.We use a similar data augmentation to YOLO and SSD with
random crops, color shifting, etC. We use the same training
strategy on COCO and VOC.

这是论文给出的原话,至于如何匹配正负样本,如何计算误差等细节并没有多讲,我将会在YOLO V3部分具体分析。

# 5.YOLO V3

## 5.1 网络结构改进

### 1. BackBone: Darknet-53

- 作者对YOLO v2时期的`Darknet-19`进行了升级,借鉴了`ResNet`的**残差单元**，**在加深网络层数提高精度的同时大大降低计算量**，接下来我们来具体剖析，先上backbone的结构图：
	![](/image/目标检测之YOLO理解与总结/15.png)
- 我们可以看到在**Darknet-53**中)，与**Darknet-19**最大的不同是没有池化层和全连接层,张量是通过卷积层来实现**下采样**的。比如stride=(2, 2)，这就等于将图像边长缩小了一半，整个特征图缩小$ 2^2 $ 倍。在`YOLO v3`中，最终要经历5次缩小，会将特征图缩小到原输入尺寸的$ \frac {1} {2^5} $ ，即$ \frac {1} {32} $。输入为**416×416** ，则输出为**13×13(416/32 = 13)** 。需要注意的是，最后三层`Avgpool、Connected`和 `softmax layer`是用于在 `Imagenet`数据集上作分类训练用的。当我们用 `Darknet-53` 层对图片提取特征时，是不会用到这三层的。

- 这个backbone的效果也非常好，**比起同精度的ResNet-152速度也是它的两倍**，具体见下图：
	

![](/image/目标检测之YOLO理解与总结/16.png)

### 2.整体网络架构
![](/image/目标检测之YOLO理解与总结/17.png)

## 5.2 目标边界框的预测

YOLO V3的边界框预测与YOLO V2一样，如下图所示
![](/image/目标检测之YOLO理解与总结/18.png)

> 将tx，ty传入sigmoid函数能够限制范围在0-1内，避免中心点超出网格

## 5.3 正负样本的匹配

> YOLOv3 predicts an objectness score for each bounding
box using logistic regression. This should be 1 if the bound-
ing box prior overlaps a ground truth object by more than
any other bounding box prior. If the bounding box prior
is not the best but does overlap a ground truth object by
more than some threshold we ignore the prediction, follow-
ing [17]. We use the threshold of .5. Unlike [17] our system
only assigns one bounding box prior for each ground truth
object. If a bounding box prior is not assigned to a ground
truth object it incurs no loss for coordinate or class predic-
tions, only objectness.

从原论文可知，**YOLO V3**会为每个`ground truth`分配一个正样本，也就是一张图片有几个`ground truth`，就会有几个正样本。对于每个`ground truth`，会将与它重叠最大，也就是IoU最大的`bounding box`作为正样本。对于IoU大于设定的阈值,但不是最大的`bounding box`，将会直接丢弃。如果 某个`bounding box`没有被分配为正样本，那么它没有定位损失，类别损失，仅仅有`objectness loss(置信度的损失)`。

## 5.4 损失函数的计算

YOLO V3损失函数计算如下图所示

![](/image/目标检测之YOLO理解与总结/19.png)

### 5.4.1 目标置信度损失

![](/image/目标检测之YOLO理解与总结/20.png)

置信度损失用的是**二值交叉熵损失(BCE)**，其中o$ _{i} $∈[0,1]，表示预测目标边界框与真实目标边界框的**IoU**，c为预测值，$ \hat{C}_{i} $为c通过Sigmoid函数得到的预测置信度，N为正负样本个数。
### 5.4.2 目标类别损失

![](/image/目标检测之YOLO理解与总结/21.png)
类别损失用的也是**二值交叉熵损失(BCE)**，其中O$ _{ij} $∈{0, 1}，表示预测目标边界框i中是否存在第j类目标，C$ _{ij} $为预测值，$ \hat{C}_{ij} $为C$ _{ij} $通过Sigmoid函数得到的目标概率，N$ _{pos} $为正样本个数

### 5.4.3 目标定位损失

![](/image/目标检测之YOLO理解与总结/22.png)

> 其中左下角的g就是右图中的b