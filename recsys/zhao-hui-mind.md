# 召回 - MIND

## 目标

*   MIND - Multi-Interest Network with Dynamic routing

    通过构建多种类的用户表征向量来捕捉用户不同类型的兴趣， 比如不同类目商品（衣服， 零食，电器等等）， 弥补过去电商用单个用户向量捕捉用户信息不足的缺点

## 改动点

* 基于胶囊网路(Capsule Network)和动态路由(Dynamic Routing)设计Multi-Interest Extractor  对用户不同兴趣进行聚类得到多种兴趣信息
* 设计label-aware attention 辅助用户多兴趣向量学习



## 思路

*   问题定义：

    <figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

    * $$P_u , I_u , F_i$$  分别代表用户画像属性特征， 用户行为序列特征， item商品的特征(可以是id 或其他连续离散统计值)
    * $$Vu = (v^1_u, v^2_u, ..v^k_u) = f_{user}(I_u, P_u)$$ 代表用户信息聚合后的多目标兴趣向量列表， $$v^i_u$$ 代表第i个兴趣向量。
    * $$e^i =f(F_i)$$ 代表第i个商品向量
    * 优化目标 $$f_{score} (V_u , e_i)= \max(v^k_u, e^i)$$ 让第i 个商品向量和用户第k个商品兴趣向量相似度打分最大化


* Dynaimc Routing 参数更新
  * 个人认为B2I dynamic routing 的思想和attention思想差不多，也是
    * 算user emb uj和item embedding ei 的相似度 bij 和 对应的softmax 分wij
    * 然后用这个相似度分和通过矩阵S映射到同一空间的item emb 做weight sum&#x20;
    * 再通过squeeze 函数做非线性变换 (个人认为这squeeze非线性变化也可以理解成对emb的norm)
    *   然后重复多次K 次计算把每个兴趣都遍历算得到最终的embedding

        <figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

## 优缺点







{% embed url="https://zhuanlan.zhihu.com/p/76495890" %}

{% embed url="https://arxiv.org/pdf/1904.08030v1" %}
