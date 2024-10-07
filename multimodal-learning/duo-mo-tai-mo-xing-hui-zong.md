# 多模态模型汇总

## 多模态模型类型



### Image+Text

* CLIP (Contrastive Language-Image Pre-training)
  * 思路：CLIP 目的还是文本和图像特征对齐， 主要思路是把image encoder(可以是ResNet或n ViT 作为backbone) 和Text encoder(一般是bert) 的embedding提取并归一化。 之后把两者的embedding做余弦相似度。text-image pair是匹对的话相似度接近1， 不匹对的接近0.用这种方法去训练模型。 CLIP对图文预测时可以直接用 zero-shot 方式直接预测， 或者可以通过fine-tunning形式去用新文本图片pair 微调
  * 训练：CLIP使用对比学习的infoNCE loss![](<../.gitbook/assets/image (24).png>), 大概思路就是把正确匹对的image-text pair 和其他不匹对的样本放在一起做softmax， 让匹对的正样本预测值大的越大， 其他不匹对的样本预测值越小。 loss里面加了temperature， 作用是控制logits的分布形状，对于既定的[logits](https://zhida.zhihu.com/search?content\_id=233440332\&content\_type=Article\&match\_order=3\&q=logits\&zhida\_source=entity)分布的形状，当temp值变大，则(q\*k)/temp变小，指数运算之后更小，导致原来的[logits分布](https://zhida.zhihu.com/search?content\_id=233440332\&content\_type=Article\&match\_order=2\&q=logits%E5%88%86%E5%B8%83\&zhida\_source=entity)更平滑
  * 优缺点：
    *   CLIP在训练时用了大量v100 GPU和400million的image-text pair对进行训练了10多天， 消耗资源很大， 一般都是用来模型微调

        <figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>
* BLIP
* BLIP2
* BLIP + Instruction Tunning
* Visual Instruct Tunning - LLaVA
*



### Video+Text



### Audio+Text



### Multimodal For RecSys









小红书多模态搜推应用

{% embed url="https://www.bilibili.com/video/BV19W4y1f7Nw/?vd_source=1974b2589f7687d5f2267c89b7351f7b" %}





{% embed url="https://arxiv.org/abs/2401.13601" %}

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://github.com/zchoi/Multi-Modal-Large-Language-Learning?tab=readme-ov-file" %}

{% embed url="https://segmentfault.com/a/1190000044778456" %}

{% embed url="https://arxiv.org/abs/2103.00020" %}
