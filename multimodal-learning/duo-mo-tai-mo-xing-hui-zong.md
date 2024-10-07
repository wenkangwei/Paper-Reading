# 多模态模型汇总

## 多模态模型类型



### Image+Text

* CLIP (Contrastive Language-Image Pre-training)
  * 思路：CLIP 目的还是文本和图像特征对齐， 主要思路是把image encoder(可以是ResNet或n ViT 作为backbone) 和Text encoder(一般是bert) 的embedding提取并归一化。 之后把两者的embedding做余弦相似度。text-image pair是匹对的话相似度接近1， 不匹对的接近0.用这种方法去训练模型。 CLIP对图文预测时可以直接用 zero-shot 方式直接预测， 或者可以通过fine-tunning形式去用新文本图片pair 微调
  * 训练：CLIP使用对比学习的infoNCE loss![](<../.gitbook/assets/image (24).png>), 大概思路就是把正确匹对的image-text pair 和其他不匹对的样本放在一起做softmax， 让匹对的正样本预测值大的越大， 其他不匹对的样本预测值越小。 loss里面加了temperature， 作用是控制logits的分布形状，对于既定的[logits](https://zhida.zhihu.com/search?content\_id=233440332\&content\_type=Article\&match\_order=3\&q=logits\&zhida\_source=entity)分布的形状，当temp值变大，则(q\*k)/temp变小，指数运算之后更小，导致原来的[logits分布](https://zhida.zhihu.com/search?content\_id=233440332\&content\_type=Article\&match\_order=2\&q=logits%E5%88%86%E5%B8%83\&zhida\_source=entity)更平滑
  * 优缺点：
    *   CLIP在训练时用了大量v100 GPU和400million的image-text pair对进行训练了10多天， 消耗资源很大， 一般都是用来模型微调

        <figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>
* BLIP: Bootstrapping Language-Image Pre-training for Unified Vision-Language Understanding and Generation
  * 思路：
    * 模型结构
      * 图像块的编码（ViT）：图像打成patches块后进行编码，增加cls token来记录全局的特征（cls作用类似位置编码 用到每个句子头部，保留patches的空间特征）
      * 文本的编码（BERT）：对句子进行编码，增加cls token记录句子的全局特征
      * Image-grounded text encoder：在文本embedding中注入了图像特征，通过在self-attention和FFN中间增加一层cross-attention来对齐text-encoder和img-encoder的特征。
      * Image-grounded text decoder：用causal self-attention层（预测下一个token）代替了双向自注意力层（建立当前输入token的表达）和左边的encoder共享除了self-attention之外的参数
    * loss:
      * ITC image-text contrastive: 类似CLIP的对比学习loss， 让相同text-image pair相似度最大化
      * ITM image-text matching: 用于细粒度区分正负样本对， 负样本使用hard-negative mining得到
      * LM language model: 优化image-grounded text decoder，学习如何从给定图生成连贯的文本描述，采用交叉熵代价函数以自回归方式最大化对应文本概率
  * 优缺点
    * 图像编码器只推理1次， 比较耗时， 文本编码器推理3次， 计算很复杂
  *   图解

      <figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>
* BLIP2:  Bootstrapping Language-Image Pre-training with Frozen Image Encoders and Large Language Models （image 2 text）
  * 思路： 分两个阶段，通过利用预训练好的视觉模型和语言模型来提升多模态效果和降低训练成本。
    * [BLIP-2](https://zhida.zhihu.com/search?content\_id=233440332\&content\_type=Article\&match\_order=1\&q=BLIP-2\&zhida\_source=entity) 由预训练的Image Encoder，预训练的Large Language Model，和一个可学习的 Q-Former 组成。
      * Image Encoder：从输入图片中提取视觉特征，尝试了两种网络结构，CLIP 训练的 ViT-L/14和[EVA-CLIP](https://zhida.zhihu.com/search?content\_id=233440332\&content\_type=Article\&match\_order=1\&q=EVA-CLIP\&zhida\_source=entity)训练的 ViT-g/14（去掉了最后一层）。
      * Large Language Model：大语言模型进行文本生成，尝试了接入decoder-based LLM 和 encoder-decoder-based LLM两种结构。
      * Q-Former：弥补视觉和语言两种模态的modality gap，可以理解为固定图像编码器和固定LLM之间的信息枢纽，选取最有用的视觉特征给LLM来生成文
    * 其中Q-former (query former) 由Image Transformer和Text Transformer两个子模块构成，它们共享相同自注意力层。
    * Image Transformer：通过和image encoder交互来提取视觉特征，输入是一系列（文中用的32个\*768长度）可学习的 Queries，这些Query通过自注意力层相互交互，并通过交叉注意力层与冻结的图像特征交互，还可以通过共享的自注意力层与文本进行交互；输出的query尺寸是32\*768，远小于冻结的图像特征257\*1024(ViT-L/14)。
    * Text Transformer：既作为[文本编码器](https://zhida.zhihu.com/search?content\_id=233440332\&content\_type=Article\&match\_order=3\&q=%E6%96%87%E6%9C%AC%E7%BC%96%E7%A0%81%E5%99%A8\&zhida\_source=entity)也作为文本解码器，它的自注意力层与Image Transformer共享，根据预训练任务，用不同的self-attention [masks](https://zhida.zhihu.com/search?content\_id=233440332\&content\_type=Article\&match\_order=1\&q=masks\&zhida\_source=entity)来控制Query和文本的交互方式。
    *

        <figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>
  * 优缺点
    * Q-former计算复杂度还是有些高， 而且到了后期有人发现用简单的MLP也可以效果比较好， 后面Q-former用的比较少
  *   图解

      <figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>





* Visual Instruct Tunning - LLaVA Large (Language and Vision Assistant)
  * 一种连接视觉编码器和LLM的端到端训练多模态大模型。
  * 使用视觉编码器CLIP ViT-L/14+语言解码器LLaMA构成多模态大模型，然后使用生成的数据进行指令微调
  *   把一张图片经过vision encoder得到图片的token， 然后再经过一个映射函数把图像token映射到文本token的空间。 这个映射关系可以是简单的线性映射，也可以是cross-attention等复杂映射。

      <figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>
* 训练步骤
* 训练方式是典型的next token prediction, 给定query Xq, 指令 X\_instruct， 图片token信息 Xv, 以及 answer信息 Xa 去预测现在的token xi 输出概率
*   ![](<../.gitbook/assets/image (30).png>)

    <figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>


* 训练步骤：
  1. 预训练特征对齐模块（映射层W），冻结视觉编码器和LLM，只训练映射矩阵W得到上面公式的最大似然，相当于为冻结的 LLM 训练一个适配的visual tokenizer。
  2. 端到端的微调语言模型+映射层。





### Multimodal For RecSys









小红书多模态搜推应用

{% embed url="https://www.bilibili.com/video/BV19W4y1f7Nw/?vd_source=1974b2589f7687d5f2267c89b7351f7b" %}





{% embed url="https://arxiv.org/abs/2401.13601" %}

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://github.com/zchoi/Multi-Modal-Large-Language-Learning?tab=readme-ov-file" %}

{% embed url="https://segmentfault.com/a/1190000044778456" %}

{% embed url="https://arxiv.org/abs/2201.12086" %}

{% embed url="https://arxiv.org/abs/2103.00020" %}

{% embed url="https://arxiv.org/pdf/2301.12597" %}
