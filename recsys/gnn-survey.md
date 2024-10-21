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

![](<../.gitbook/assets/image (2) (1) (1) (1).png>)

#### Sequential Recommendation

sequential recommendation (序列推荐) 是假设用户的喜好上动态变化的, 这点和一般的推荐算法思路想法. Sequential recommendation 根据用户的历史行为去预测用户是否会和要推荐的商品进行互动.&#x20;

根据用户是否匿名以及行为是否被切分到不同的会话窗(session) , Sequential recommendation 也可以细分为 sequential recommendation, session-based recommendation.

推荐目标为:  给定一个序列$$s^u$$ 第$$i_{s,n+1}$$ 商品的推荐概率

![](<../.gitbook/assets/image (3) (1) (1) (1).png>)

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

![](<../.gitbook/assets/image (10).png>)



#### social network enhanced

**特点:** 这类GNN考虑用户之间的社交关系的相互影响 (influence of friends ), 以及 多个用户的兴趣的整合(Preference integration).

**方法**: 这类GNN模型把用户的社交网络图结合到user-item bi-graph 里面, 而结合的方法有以下两种:

a) 把social network 和bi-graph 分开独立训练GNN, 之后再把embedding结合起来

**优点:**  user-item bi-graph的embedding可以直接拿出来用,而社交同质化网络的embedding是在模拟用户影响行为的变化过程,可以用来挖掘用户之间的关系

![](<../.gitbook/assets/image (7).png>)



b) 把social network, bi-graph 通过合并以及采样直接输入到GNN得到整合后的node embedding

优点: 用户社交影响和用户的兴趣能同时反映到一个模型里面

![](<../.gitbook/assets/image (5) (1).png>)

#### knowledge graph  enhanced

优点: 这类GNN 借助知识谱图knowledge graph(KG)的结构, 把KG里面的语意解释结合到图里面, 提高了模型的可解释性

缺点: KG需要一定的domain knowledge 领域知识, 而且需要人工去搭建, 人工成本高

挑战:&#x20;

1. graph construction: 怎么去构建图结构去平衡简单性和信息度, 图结构越简单,能保存的信息越少
2. relation-aware aggregation: KG里面有多种边和关系, 怎么对应这些关系去建模聚合信息

### 4.  Sequential Recommendation

Sequential Recommendation 的推荐框架可以总结为如下:

![](<../.gitbook/assets/image (9).png>)

步骤有

1. 从序列构建图
2. 把图输入GNN block进行embedding学习
3. 通过序列模型对输入的序列的embedding进行聚合得到整合后的序列embedding
4. 用序列embedding做任务推理

参考文章:&#x20;

![](<../.gitbook/assets/image (8) (1).png>)



思考:

1. 出于 sequential model 不能并行计算加快计算速度, 是否能够把每个时刻的的输入增加对应的weight, 而模型会把当前的时刻t 也作为输入, 这个weight会基于t的变化而变化从而改变每个时刻输入的权重? 之后我们可以定时把某一段时间内的输入聚合到一起得到一个batch从而达到并行计算效果?

#### social network enhanced

和上面第3节一样, 可以把social network 结合到graph里面

#### knowledge graph  enhanced

和上面第3节一样, 可以把KG结合到graph里面提高可解释性

### 5. Other Task

#### CTR prediction

点击率预测任务

#### Point of interest recommendation(POI)

Points-of-interest (POI) recommendation plays a key role in **location-based** service. Owing to the **spatial and temporal** characteristics, POI recommendation should model the geographical influence among POIs and the transition patterns from users’ sequential behaviors

POI考虑到场景地点以及时间对用户兴趣的影响而进行推荐， 比如网红在某些地点打卡， 会推荐和氛围背景相对应的商品。或者旅游时推荐当地特产之类的推荐方式

#### **Group recommendation**

Group recommendation aims to suggest items to a group of users instead of an individual one based on their historical behaviors

向一群人同时推荐商品， 而不是单个人推荐， 相当于分人群推荐



**Bundle recommendation**&#x20;

Bundle recommendation aims to recommend a set of items as a whole for a user. For group recommendation, “group” is made up of users; for bundle recommendation, “group” means a set of items.

结合衣服商品推荐领域, 是否可以推荐衣服组合, 以及互补商品组合, 解决选择困难症用户问题?



**参考文章:**

![](<../.gitbook/assets/image (11).png>)

### 6. Other ideas

### 7. Reference

{% file src="../.gitbook/assets/GraphNeuralNetworksInRecommendationSystem.pdf" %}

