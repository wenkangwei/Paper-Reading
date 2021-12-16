# Graph Neural Networks in Recommender Systems: A Survey

### Graph Neural Networks in Recommender Systems: A Survey

### 1. Background

这篇文章是一篇由北京大学和阿里团队在2021 ACM发表的 survey,调查了过去不同推荐算法并按照不同任务对GNN的模型进行分类

### 2. Method/ idea

#### Recommendation System

推荐系统的任务一般分为两类， 一类是一般推荐系统General Recommendation， 另一类是序列推荐 Sequential Recommendation。 General Recommendation假设用户的兴趣是固定不变的， 而Sequential Recommendation则相反，它假设用户的兴趣是动态的。 而其中 Session-based Recommendation 基于会话窗的推荐则属于 Sequential Recommendation中的一个子类。

#### General Recommendation

General Recommendation 一般推荐系统通常假设用户会有固定的兴趣爱好 并且会对用户的隐性或显性的反馈进行建模来预测用户可能喜欢的商品.  General Recommendation 可以分为3种: user-item interaction , social network enhanced, knowledge graph enhanced.&#x20;

打分方式:  输入用户特征 $$h_{u}^*$$, 商品特征$$h_{i}^*$$ , 计算用户对商品的得分y

![](<../.gitbook/assets/image (2).png>)

#### Sequential Recommendation

sequential recommendation (序列推荐) 是假设用户的喜好上动态变化的, 这点和一般的推荐算法思路想法. Sequential recommendation 根据用户的历史行为去预测用户是否会和要推荐的商品进行互动.&#x20;

根据用户是否匿名以及行为是否被切分到不同的会话窗(session) , Sequential recommendation 也可以细分为 sequential recommendation, session-based recommendation.

推荐目标为:  给定一个序列$$s^u$$ 第$$i_{s,n+1}$$ 商品的推荐概率

![](<../.gitbook/assets/image (3).png>)

####

### 3.  General Recommendation

#### 3.1 user-item interaction

**特点:** 这类方法考虑怎么利用GNN去充分传递互动的item, user之间的信息,提高bi-graph的user, item的representation的性能. 这类模型的算法流程基本如下:

![](<../.gitbook/assets/image (6).png>)



**难点**有一下几点

1. graph construction: 怎么去构建bi-graph 图,以及sample neighbor, 特别上图特别大的时候
2. neighbor aggregation : 怎么去聚集邻居节点的信息? mean-pooling? sum pooling ? 等等
3. information update: 怎么聚合中心节点和邻居节点聚合的信息
4. final node representation: 怎么去用node  representation去预测最后的task

这几点是传统GNN的node  representation的更新的最基本的组成部分. 不过在推荐里面, 最常见的是bi-graph类型的图

参考文章:

![](<../.gitbook/assets/image (8).png>)



#### social network enhanced

**特点:** 这类GNN考虑用户之间的社交关系的相互影响 (influence of friends ), 以及 多个用户的兴趣的整合(Preference integration).

**方法**: 这类GNN模型把用户的社交网络图结合到user-item bi-graph 里面, 而结合的方法有以下两种:

a. 把social network 和bi-graph 分开独立训练GNN, 之后再把embedding结合起来

b) 把social network, bi-graph 通过合并以及采样直接输入到GNN得到整合后的node embedding

![](<../.gitbook/assets/image (7).png>)

![](<../.gitbook/assets/image (5).png>)

#### knowledge graph  enhanced



### 4.  Sequential Recommendation



### 5.Cases

### 6. Other ideas

### 5. Reference

{% file src="../.gitbook/assets/GraphNeuralNetworksInRecommendationSystem.pdf" %}

