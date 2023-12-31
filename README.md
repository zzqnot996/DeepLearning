# learn_deeplearning

## 对比学习（Contrastive Learning）:研究进展精要:  

网址 ：https://zhuanlan.zhihu.com/p/367290573

   胖胖大海:

   基于负例的对比学习（Contrastive Learning）与度量学习（Metric Learning）的异同点：

相同点：
1、对比学习和度量学习中都使用了正样本对和负样本对的概念，在训练时都是通过拉近正样本之间的距离（或者提升相似度），推远负样本之间的距离（或者降低相似度）完成模型训练
2、对比学习和度量学习通常都可以采用Backone + Projector + Loss的架构，其中对比学习的损失可以是InfoNCE Loss，度量学习的损失可以是Triplet Loss、Circle Loss、Proxy Loss、InfoNCE Loss等，个人理解在Loss层面两者可以混用

不同点：
1、任务类型：对比学习通常是自监督self-supervised任务，度量学习通常是监督supervised任务
2、训练数据构造：对比学习的训练Batch中的正样本对通常来自于同一张图像的不同增强，负样本对来自于其他图像。度量学习的训练Batch中的正样本对通常来自于属于同一类的图像，负样本对来自于属于其他类的图像
3、训练目标：对比学习使用自监督的方式，通常是为了得到一个预训练模型，训练时采用Backone + Projector + InfoNCE Loss架构，使用时只需要Backbone部分,归根结底还是要训练一个更通用、更底层的图像特征提取模型Backbone。度量学习使用有监督的方式，使用Backone + Projector + Metric Learning Loss（也可以是InfoNCE Loss）架构，最终的目的是想端到端的学习一个直接可用的embedding特征模型。训练时采用Backone + Projector + Loss架构，使用时需要Backbone + Projector部分

问题：
如果对于两张都是狗的图片（但是是不同的图片），为何他们在对比学习中，projector之后的表示空间是相邻的呢？因为他们是不同的图片，所以应该是反例，反例的话是要被在超平面球上推开，因此是离远的趋势，但为什么他们最后还是在相临近的位置上/相似度较高呢（对比其他的类别的图片，比如说猫）？ 也就是说，无监督学习是如何在表示空间中学习到不同类别形成的的clusters呢？
解释一：
对比学习使用自监督的方式，归根结底还是要训练一个更通用、更底层的图像特征提取模型Backbone，而度量学习使用有监督的方式，最终的目的是想端到端的学习一个embedding特征模型。所以对于Batch中两张狗的图像得到的特征是不是在超球面上的相邻位置对于对比学习来说并不重要，这是度量学习要关注的事情。度量学习的是有监督的，训练Batch中同一类（多张狗的图片）之间互为正样本对，不同类之间互为负样本对，保证学到的embedding特征同一类在超球面上处于相邻的位置

解释二：
以基于Batch的负例方法为例，考虑两种情况：
情况一：假如在一个Batch中的所有图片都是不同类别的（这种训练数据很难构造），那么经过两次数据增强后，有两个样本互为正例，其他2N - 2个样本都是负例，训练时拉近正例，推远负例；
情况二：假如在一个Batch中同时存在两张狗的图片，按照自监督的预设前提，自己只和自己像，和其他都不像，那么这两张狗的图片互为负样本，模型训练时在特征空间上确实会将这两张图片的表征距离拉远，所以此时需要用到损失函数里面的温度系数，温度系数设置的越小，会把“负例”推的越远，但此时的负例可能是假负例，所以作者也讲了，温度系数设置的太小容易出现误判的负例。

参考文献：

训练数据偏置（Bias）问题   CASTing Your Model:Learning to Localize Improves Self-Supervised Representations
更好构建正例的方法  Demystifying Contrastive Self-Supervised Learning:Invariances, Augmentations and Dataset Biases
像素级学习能力  Dense Contrastive Learning for Self-Supervised Visual Pre-Training

