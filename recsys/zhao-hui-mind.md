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
  *   个人认为B2I dynamic routing 的思想和attention思想差不多，也是

      * 算user emb uj和item embedding ei 的相似度 bij 和 对应的softmax 分wij
      * 然后用这个相似度分和通过矩阵S映射到同一空间的item emb 做weight sum&#x20;
      * 再通过squeeze 函数做非线性变换 (个人认为这squeeze非线性变化也可以理解成对emb的norm)![](../.gitbook/assets/image.png)
      * 然后重复多次K 次计算把每个兴趣都遍历算得到最终的embedding


  *   计算步骤

      *   定义：

          * &#x20;$$e_i$$ 第i个商品向量，对应一个商品embedding矩阵的第i行.&#x20;
          * &#x20;$$u_j$$ 第j个兴趣胶囊 的向量 (uj 向量有ei, S, bij 这些参数变量计算得到， 没有自己独立变量).&#x20;
          * &#x20;$$b_{ij}$$  为第i个商品向量和第j个兴趣胶囊向量之间的相似度，bij会作为一个变量存放到一个矩阵里面。
          *   &#x20;S 是一个共享的双线性映射矩阵把商品行为序列向量ei 和胶囊uj向量映射到同一个空间。




      * 步骤：
        * 初始化变量item 向量矩阵， 矩阵S， 相似度矩阵B, bij 对应B的第i行第j列的变量
        * 把第i行的相似度矩阵bij 做softmax 得 第i个商品和第j个兴趣胶囊相似度 $$w_{ij}$$
        * 计算第j个胶囊向量中间态： $$z_j = \sum_i w_{ij} S e_i$$， 其中$$Se_i$$ 对应胶囊网络中的low capsule 向量
        * 第j个兴趣胶囊向量： $$u_j = squash(z_j)$$
        * 用新的uj, ei 更新当前胶囊的bij： $$b_{ij}=b_{ij} + u_j S e_i$$&#x20;
        * 遍历r 遍， 每遍都对每个胶囊logit 相似度矩阵更新直到bij收敛。&#x20;
        * 注意，上面遍历过程中bij 的矩阵不是通过梯度回传学习的， 而是通过上面更新公式学习。
        *   bij收敛完后， 能直接把bij ， wij固定， 然后直接算 $$u_j$$

            &#x20;





      <figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>
*   Label-Aware Attention

    * label-aware attention 结构如下， 从结构上面来说其实是一个简单的dot-product attention. query 是 target item向量， 而value和 key 都是多兴趣向量 $$V_u =(u_1, u_2,,,u_j ..u_k)$$ 是k个兴趣向量。(注： 平常attention的key, value输入的序列是用户行为的序列， 不是兴趣序列)

    ![](<../.gitbook/assets/image (34).png>)
*   label-aware attention公式如下， 其中p 是一个可以理解成温度系数， 当p>1 时， 由于 $$V_ue_i$$

    是一个cos相似度范围在【-1， 1】， 当p 越大， power之后的相似度分之间差异越大， softmax后的得分的分布越陡， 即越尖锐， 能放大attention效果。 当p 到无限大极端情况， attention会变成hard attention只关注得分最大的兴趣embedding

    <figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

## 优缺点

* 能捕捉用户不同兴趣的emb
* 但是每次训练和推理都要在layer内部算 r次遍历使bij收敛， 会比较耗时

## 代码

{% embed url="https://github.com/PaddlePaddle/PaddleRec/blob/release/2.1.0/models/recall/mind/net.py" %}

{% embed url="https://github.com/Wang-Yu-Qing/MIND/blob/main/model.py" %}

{% embed url="https://zhuanlan.zhihu.com/p/76495890" %}

{% embed url="https://arxiv.org/pdf/1904.08030v1" %}
