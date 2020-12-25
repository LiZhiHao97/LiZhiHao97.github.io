---
layout: kaggle实战：Dog Breed Identification
title: kaggle实战：Dog Breed Identification
date: 2020-11-11 21:26:20
index_img: https://i.loli.net/2020/12/25/wJrkONyRIaKn1Dq.png
categories:
 - 计算机视觉
tags:
  - pytorch 
  - 深度学习
  - kaggle
  - 迁移学习
---

# 前言

本文将使用pytorch框架解决kaggle竞赛中犬种识别挑战的问题，该比赛的数据集为ImageNet数据集的子集，在本次挑战中，我们将尝试分类120种不同的犬种

> 竞赛网址：https://www.kaggle.com/c/dog-breed-identification
> 数据集可在该网址获取

# 导入相关模块

``` python
import torch
import torch.nn as nn
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms
import torchvision.models as models
import os
import shutil
import time
import pandas as pd
import random
```

# 设置随机数种子

``` python
random.seed(0)
torch.manual_seed(0)
torch.cuda.manual_seed(0)
```

# 整理数据集

``` python
data_dir = './dog-breed-identification'  # 数据集目录
label_file, train_dir, test_dir = 'labels.csv', 'train', 'test'  # data_dir中的文件夹、文件
new_data_dir = './train_valid_test'  # 整理之后的数据存放的目录
valid_ratio = 0.1  # 验证集所占比例
```

``` python
def mkdir_if_not_exist(path):
  # 若目录path不存在，则创建目录
  if not os.path.exists(os.path.join(*path)):
    os.makedirs(os.path.join(*path))
        
def reorg_dog_data(data_dir, label_file, train_dir, test_dir, new_data_dir, valid_ratio):
  # 读取训练数据标签
  labels = pd.read_csv(os.path.join(data_dir, label_file))
  id2label = {Id: label for Id, label in labels.values}  # (key: value): (id: label)

  # 随机打乱训练数据
  train_files = os.listdir(os.path.join(data_dir, train_dir))
  random.shuffle(train_files)    

  # 原训练集
  valid_ds_size = int(len(train_files) * valid_ratio)  # 验证集大小
  for i, file in enumerate(train_files):
    img_id = file.split('.')[0]  # file是形式为id.jpg的字符串
    img_label = id2label[img_id]
    if i < valid_ds_size:
      mkdir_if_not_exist([new_data_dir, 'valid', img_label])
      shutil.copy(os.path.join(data_dir, train_dir, file),
            os.path.join(new_data_dir, 'valid', img_label))
    else:
      mkdir_if_not_exist([new_data_dir, 'train', img_label])
      shutil.copy(os.path.join(data_dir, train_dir, file),
            os.path.join(new_data_dir, 'train', img_label))
    mkdir_if_not_exist([new_data_dir, 'train_valid', img_label])
    shutil.copy(os.path.join(data_dir, train_dir, file),
          os.path.join(new_data_dir, 'train_valid', img_label))

  # 测试集
  mkdir_if_not_exist([new_data_dir, 'test', 'unknown'])
  for test_file in os.listdir(os.path.join(data_dir, test_dir)):
    shutil.copy(os.path.join(data_dir, test_dir, test_file),
          os.path.join(new_data_dir, 'test', 'unknown'))
```
``` python
reorg_dog_data(data_dir, label_file, train_dir, test_dir, new_data_dir, valid_ratio)
```
# 图像增广

``` python
transform_train = transforms.Compose([
  # 随机对图像裁剪出面积为原图像面积0.08~1倍、且高和宽之比在3/4~4/3的图像，再放缩为高和宽均为224像素的新图像
  transforms.RandomResizedCrop(224, scale=(0.08, 1.0), ratio=(3.0/4.0, 4.0/3.0)),
  # 以0.5的概率随机水平翻转
  transforms.RandomHorizontalFlip(),
  # 随机更改亮度、对比度和饱和度
  transforms.ColorJitter(brightness=0.4, contrast=0.4, saturation=0.4),
  transforms.ToTensor(),
  # 对各个通道做标准化，(0.485, 0.456, 0.406)和(0.229, 0.224, 0.225)是在ImageNet上计算得的各通道均值与方差
  transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])  # ImageNet上的均值和方差
])
```
``` python
# 在测试集上的图像增强只做确定性的操作
transform_test = transforms.Compose([
  transforms.Resize(256),
  # 将图像中央的高和宽均为224的正方形区域裁剪出来
  transforms.CenterCrop(224),
  transforms.ToTensor(),
  transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])
])

```

# 数据集导入

``` python
train_ds = torchvision.datasets.ImageFolder(root=os.path.join(new_data_dir, 'train'),
                       transform=transform_train)
valid_ds = torchvision.datasets.ImageFolder(root=os.path.join(new_data_dir, 'valid'),
                       transform=transform_test)
train_valid_ds = torchvision.datasets.ImageFolder(root=os.path.join(new_data_dir, 'train_valid'),
                       transform=transform_train)
test_ds = torchvision.datasets.ImageFolder(root=os.path.join(new_data_dir, 'test'),
                       transform=transform_test)

batch_size = 128
train_iter = torch.utils.data.DataLoader(train_ds, batch_size=batch_size, shuffle=True)
valid_iter = torch.utils.data.DataLoader(valid_ds, batch_size=batch_size, shuffle=True)
train_valid_iter = torch.utils.data.DataLoader(train_valid_ds, batch_size=batch_size, shuffle=True)
test_iter = torch.utils.data.DataLoader(test_ds, batch_size=batch_size, shuffle=False)
```

# 定义模型

由于此竞赛使用了ImageNet数据集的子集，我们可以使用迁移学习的方法，使用在ImageNet完整数据集上预训练的模型来抽取图像特征。

此处我们使用预训练的ResNet-34模型，直接复用预训练模型在输出层的输入，即抽取的特征，然后我们重新定义输出层，本次我们仅对重定义的输出层的参数进行训练，而对于用于抽取特征的部分，我们保留预训练模型的参数。

``` python
def get_net(device):
  finetune_net = models.resnet34(pretrained=False)  # 预训练的resnet34网络
  finetune_net.load_state_dict(torch.load('./dog-breed-identification/resnet34-333f7ec4.pth'))
  for param in finetune_net.parameters():  # 冻结参数
      param.requires_grad = False
  # 原finetune_net.fc是一个输入单元数为512，输出单元数为1000的全连接层
  # 替换掉原finetune_net.fc，新finetuen_net.fc中的模型参数会记录梯度
  finetune_net.fc = nn.Sequential(
      nn.Linear(in_features=512, out_features=256),
      nn.ReLU(),
      nn.Linear(in_features=256, out_features=120)  # 120是输出类别数
  )
  return finetune_net
```
> 若将pretrained设为true，会自动下载预训练好的resnet34网络，但是速度较慢，且容易断，还没断点续传功能
>  resnet34-333f7ec4.pth可在网络中搜素下载

# 定义训练函数

``` python
def evaluate_loss_acc(data_iter, net, device):
  loss = nn.CrossEntropyLoss()
  is_training = net.training
  net.eval()
  l_sum, acc_sum, n = 0, 0, 0
  with torch.no_grad():
    for X, y in data_iter:
      X, y = X.to(device), y.to(device)
      y_hat = net(X)
      l = loss(y_hat, y)
      l_sum += l.item() * y.shape[0]
      acc_sum += (y_hat.argmax(dim=1) == y).sum().item()
      n += y.shape[0]
  net.train(is_training)
  return l_sum / n, acc_sum / n
```
``` python
def train(net, train_iter, valid_iter, num_epochs, lr, wd, device, lr_period,
      lr_decay):
  loss = nn.CrossEntropyLoss()
  optimizer = optim.SGD(net.fc.parameters(), lr=lr, momentum=0.9, weight_decay=wd)
  net = net.to(device)
  for epoch in range(num_epochs):
    train_l_sum, n, start = 0.0, 0, time.time()
    if epoch > 0 and epoch % lr_period == 0:  # 每lr_period个epoch，学习率衰减一次
      lr = lr * lr_decay
      for param_group in optimizer.param_groups:
        param_group['lr'] = lr
    for X, y in train_iter:
      X, y = X.to(device), y.to(device)
      optimizer.zero_grad()
      y_hat = net(X)
      l = loss(y_hat, y)
      l.backward()
      optimizer.step()
      train_l_sum += l.item() * y.shape[0]
      n += y.shape[0]
    time_s = "time %.2f sec" % (time.time() - start)
    if valid_iter is not None:
      valid_loss, valid_acc = evaluate_loss_acc(valid_iter, net, device)
      epoch_s = ("epoch %d, train loss %f, valid loss %f, valid acc %f, "
             % (epoch + 1, train_l_sum / n, valid_loss, valid_acc))
    else:
      epoch_s = ("epoch %d, train loss %f, "
             % (epoch + 1, train_l_sum / n))
    print(epoch_s + time_s + ', lr ' + str(lr))
```

# 最终超参数选择

``` python
num_epochs, lr_period, lr_decay = 20, 10, 0.1
lr, wd = 0.03, 1e-4
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
net = get_net(device)
train(net, train_iter, valid_iter, num_epochs, lr, wd, device, lr_period, lr_decay)
```

# 训练

``` python
net = get_net(device)
train(net, train_valid_iter, None, num_epochs, lr, wd, device, lr_period, lr_decay)
```

# 测试

使用训练好的模型队测试数据进行预测，并写入**submission.csv**