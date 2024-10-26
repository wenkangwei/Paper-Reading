# 召回 - MIND



## 目标

*   MIND - Multi-Interest Network with Dynamic routing

    通过构建多种类的用户表征向量来捕捉用户不同类型的兴趣， 比如不同类目商品（衣服， 零食，电器等等）， 弥补过去电商用单个用户向量捕捉用户信息不足的缺点

## 改动点

* 基于胶囊网路(Capsule Network)和动态路由(Dynamic Routing)设计Multi-Interest Extractor  对用户不同兴趣进行聚类得到多种兴趣信息
* 设计label-aware attention 辅助用户多兴趣向量学习



## 思路

* 问题定义：
*





<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

## 优缺点







{% embed url="https://zhuanlan.zhihu.com/p/76495890" %}

{% embed url="https://arxiv.org/pdf/1904.08030v1" %}
