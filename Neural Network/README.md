# 论文阅读笔记

**记录一些读过的论文，给出个人对论文的评分情况并简述论文insight**

**评分细节：** 

- 5分：值得反复阅读的论文，非常具有启发性

- 4分：值得精读的论文，具有一定的启发性

- 3分：值得粗略阅读的论文，了解论文大概思想后会有一定收获

- 2分：价值不大的论文，读完基本没有收获

- 1分：粗制滥造的论文

> 论文将按照年份/评分依次排序，绝大多数论文将会给出[arXiv](https://arxiv.org/)的链接.


# Table of contents

- [论文阅读笔记](#论文阅读笔记)
- [Table of Contents](#table-of-contents)
- [Neural Network](#neural-network)
    - [Interpretability](#interpretability)
    - [Tricks](#tricks)
    - [Hard And Easy](#hard-and-easy)
    - [Architecture](#architecture)

# Neural Network

> 有关神经网络的各类杂文，如可解释性,tricks等。

## Interpretability

> 神经网络的可解释性相关文章。

- [Do Wide and Deep Networks Learn the Same Things? Uncovering How Neural Network Representations Vary with Width and Depth](https://arxiv.org/abs/2010.15327) (ICLR2021 under review)
    - 3分
    - 实验性的文章。发现了一个'block structure'现象:即Resnet相对于数据集过参数化后,有很多laryer输出相似的特征，即是冗余的。个人感觉这个现象是比较显然的，Resnet的跳接就是想做这样的事情。文中也没有讨论别的网络是否有这样的现象(reviewer也提到了这一点)。
    - 比较有意思的一点是，文中发现较深或者较宽的网络最终虽然有着相似的acc，但是对于对于某些类，两者的acc却差很多(好而不同?有集成的潜质)。甚至在某些类上，小型网络能比大型网络做的更好。

- [Understanding deep learningrequires rethinking generalization](https://arxiv.org/abs/1611.03530) (ICLR2017_Best paper)
    - 4分
    - 实验证明了无论是输入扰动还是标签扰动，神经网络都可以百分百的拟合训练数据。那么核心问题来了，明明网络只需要暴力地去记住训练集就好，为什么实际上却有着强大的泛化能力呢?
    - 1.正则化:对泛化能力有提升，但是并不显著。
    - 2.有限样本情况下，两层Relu神经网络的表达能力。
    - 3.SGD隐式地提高了泛化能力。
    - 总的来说，文章最终也没有解释为什么，但是给人们一些新的角度重新去思考这个问题。

- [Learning Deep Features for Discriminative Localization](https://arxiv.org/abs/1512.04150) (CVPR2016)
    - 5分
    - 方法简单，广泛用于可视化模型的spatial attention。
    - 模型最后一层activation map经过GAP之后(X)再过Linear(W)层得到logits。通常认为W中的每个类向量可以表征对应的类，这篇文章则将这个类向量中的每个值对应到activation map每个channel的重要性，那么就可以对其做channel-wise的加权和，再upsample恢复到原尺寸就可以可视化模型到底关注了图像的哪些位置。

## Tricks

> 神经网络的各种tricks。

- [Bag of Tricks for Image Classification with Convolutional Neural Networks](https://arxiv.org/abs/1812.01187) (CVPR2019)
    - 3分
    - 记录了应用nn进行图像分类的各种tricks，第五章介绍的mixup,label smooth,知识蒸馏等都是非常有用的技巧。

- [mixup: BEYOND EMPIRICAL RISK MINIMIZATION](https://arxiv.org/abs/1710.09412) (ICLR2018)
    - 5分
    - mixup，实质上是给模型加上了一个线性系统的先验，实验效果非常显著。在本地跑mixup时，resnet18_cifar10可以提升一个点,resnet18_cifar100可以提升三个点。

## Hard And Easy

> 关于样本难易的相关文章。

- [Let’s Agree to Agree: Neural Networks Share Classification Order on RealDatasets](https://arxiv.org/abs/1905.10854) (ICML2020)
    - 3分
    - 实验性的文章。文中阐述的有两点发现很有意思:
    - 1.The order in which different architectures learn the data is similar.神经网络学习时，存在一个order，且不同网络结构学习的order是类似的。这是符合我们直觉的，就是一个先易后难的order。
    - 2.After convergence,we see that ResNet-50 first learns all the examples AlexNet does, then continues to learn new examples.个人感觉根据8-a图得到的结论不是原文的结论，而是在小网络达到最高acc后，大网络同时学习只有小网络分类正确的样本和新的样本。

## Architecture

> 神经网络的结构。

- [Squeeze-and-Excitation Networks](https://arxiv.org/abs/1709.01507) (CVPR2018)
    - 3分
    - SEnet:channel-wise attention。
    - 建模通道之间的关系，通过网络的全局损失函数自适应的重新矫正通道之间的特征相应强度。是一个c尺度的attention，通过global pool(->c\*1*1)+FC+sigmoid得到attention系数。对于Resnet来说，可以加入每一个Basicblock中。

- [CBAM: Convolutional Block Attention Module](https://arxiv.org/abs/1807.06521) (ECCV2018)
    - 3分
    - channel-wise attention + spatial-wise attention。论文中称两者分别为'look what'和'look where'。
    - 在SEnet的基础上加了一个spatial-wise attention。这是一个h\*w尺度的attention，通过channel-wise pool(->2\*h*w)+conv+sigmoid得到attention系数，同样可以加入每一个Basicblock中。

- [MobileNets: Efficient Convolutional Neural Networks for Mobile Vision Applications](https://arxiv.org/abs/1704.04861) (CVPR2017)
    - 4分
    - 通过depthwise separable convolution(深度可分离卷积)降低网络计算量。
    - 如一个5\*5\*16\*32(16->32:in_planes->out_planes)的卷积参数，转化为channel-wise的卷积加上一个conv1\*1将通道数扩展，即5\*5\*1\*16 + 1\*1\*16\*32。


