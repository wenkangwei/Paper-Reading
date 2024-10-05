# 知识蒸馏概述

## 背景

知识蒸馏(Knowledge Distillation, KD) 是迁移学习中的一种，主要是把1个或多个模型(又称 teacher model)的知识传授给子模型(student model)， 让子模型学习它们的知识。 这个子模型的结构为了加快模型推理速度通常要比较简单。 目前技术已经比较成熟， 并且能结合其他学习方法一起并用， 如对比学习， 表征学习等等提升模型效果。 这里简单列举说明常用的知识蒸馏的方法

## 蒸馏知识类型

### 1、Response-Based Knowledge

student model最终输出模仿teacher model的最终输出打分行为， student model打分分布逼近teacher model的分布。 使用的loss一般是BCE loss， 或者对比学习常用的infoNCE 等

优点：

1. 只要teacher模型最终打分即可， 无需强依赖teacher model模型本身， 比较适合用在teacher model经常变动的case

缺点：

1. 因为student只teacher最终输出行为， 蒸馏学习知识有限

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

### 2、Feature-Based Knowledge

student model的中间输出特征表征（intermediate representation）逼近teacher 的中间特征表征。使用的loss一般是距离loss， 或者KL loss 等

优点：

1. student 从teacher学习的知识更加多样， 可以更细粒度学习teacher知识提升效果

缺点：

1. 强依赖teacher model模型本身， 如果teacher model版本经常迭代的场景， 需要经常变更teacher model 并且学习的知识分布可能也会经常变化

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

### 3、Relation-Based Knowledge

基于关系的知识蒸馏是让student 输出的实例之间的关系学习并逼近teacher model输出的实例关系。 这种输出的关系知识可以是 实例之间的相似度矩阵，图信息， embedding等， 而非学习 实例表征本身

优点:

1. 可以让student学习更丰富的不同形式的知识，如图知识， 顺序先后关系知识(像排序的先后关系)

缺点：

1. 计算每两两实例之间的关系的计算复杂度比较高

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

## 蒸馏学习训练方法

蒸馏学习的常见训练方法分为下面3种:

1. offline distillation: 这种比较常见， 是teacher模型预训练完后， 直接推理输出label让student学习， 其中teacher是不学习不梯度回传。 这种方法比较适合teacher model 很复杂（如LLM大模型) 把类似embedding， 或者打分值给student去学习
2. online distillation: teacher和student都梯度回传学习。 这种方法一般梯度计算量比较大， 适合teacher model不能太大，并且最好要teacher model有warmup一定程度收敛再训练效果会有保证。 优点就是teacher和student同时训练， 一步走， 不用像offline distillation那样要teacher 训练完再训student
3. self-distillation: 自蒸馏学习是一个模型同时作为student和teacher 让其中一个复杂网络模块的知识输出浅层的模块里面。复杂的网络模块可以在训练完后不再使用， 仅使用浅层模块推理， 加速模型推理

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

##

## 参考

{% embed url="https://neptune.ai/blog/knowledge-distillation" %}

\[1] Distilling the Knowledge in a Neural Network. Hinton G, Vinyals O, Dean J (2015) NIPS Deep Learning and Representation Learning Workshop. [https://arxiv.org/abs/1503.02531](https://arxiv.org/abs/1503.02531)&#x20;

\[2] Model Compression. Bucilua C, Caruana R, Niculescu-Mizil A (2006) [https://dl.acm.org/doi/10.1145/1150402.1150464](https://dl.acm.org/doi/10.1145/1150402.1150464)&#x20;

\[3] Knowledge distillation: a survey. You J, Yu B, Maybank SJ, Tao D (2021)  [https://arxiv.org/abs/2006.05525](https://arxiv.org/abs/2006.05525)&#x20;

\[4] DistilBERT, a distilled version of BERT: smaller, faster, cheaper and lighter (2019) Sanh V, Debut L, Chammond J, Wolf T. [https://arxiv.org/abs/1910.01108v4](https://arxiv.org/abs/1910.01108v4)&#x20;

\[5] Lessons from building acoustic models with a million hours of speech (2019) Parthasarathi SHK, Strom N. [https://arxiv.org/abs/1904.01624](https://arxiv.org/abs/1904.01624)&#x20;
