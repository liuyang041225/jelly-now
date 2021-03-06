---
layout: post
title: Memory Network End2End
published: true
---

## End to End training your Memory Network

在介绍**端到端记忆网络**(End to End Memory Network)之前，我想先介绍一下这个技术为什么会出现，以及该技术是为了解决怎样的问题。

从技术路线来说，**端到端记忆网络**来自于**记忆网络**( J. Weston, S. Chopra, and A. Bordes. Memory networks. In International Conference on Learning Representations (ICLR), 2015)。准确地说，端到端记忆网络是记忆网络的一个改良版本。记忆网络的出现主要是为了解决一些依赖于（Long Term/Short Term,长期以及短期）记忆内容依赖的应用。

比如，在对话应用中，John与对话机器人Robt进行交谈，John的第n轮发言，显然与前n-1轮的对话内容有一定的联系（请注意这是一个假设），理由非常简单，在前n-1轮对话中，John显式或者隐式的提供了关于自己的信息，这些信息包括了他交谈的目的，他自身的兴趣，他提出的补充条件等（aim, interest, condition）。如下图所示：
![Example]({{site.baseurl}}/images/dialog.png)

* John与Robt的对话发生在上图提示的Short-Term Memories中，在第1轮的交流中，我们知道John想要找一部电影来打发晚上的时光(c)，系统回应(response)则推荐电影Shaolin Soccer给John。
* John回复Shaolin Soccer以及Kung Fu Hustle他已经看过，他想要看更多Stephen Chow的电影。
* **系统的任务就是在已知上面事实已经发生的情况下，返回给John一个比较合理的答案。**

如何实现具备这种功能的模型？这显然有别于我们在之前的内容中讨论的各种神经网络（CNN,LSTM,GRU，etc），就从原理上来说，**这些深度网络结构提供的主要是从数据中学习到更好表达的功能**(Learning better represetation from data)，而显然我们需要的是具备寻找相关事实，并进行基本推理的模型。记忆网络(Memory Networks)便提供了这些功能，因为：

* 记忆网络具有一个记忆组件可以对记忆进行读、写操作 (Class of Model that combine large memory with learning component that can **read** and **write** to it)
* 记忆网络实现了对记忆组件中内容的选择性关注，并基于这些选择性关注的内容进行推理 (Incorporates **reasoning** with **attention** over **memory** (RAM))
* 大多数机器学习算法具有一个有限的记忆，而这些记忆对一些低级的任务实际上已经足够。(参考，RNN系列模型的长短时记忆对分类的影响)

## Basic Framework of End to End Memory Network

基于上面的描述，其实并不难想象记忆网络的基本结构,如下图所示：
![End2End Memory Network]({{site.baseurl}}/images/end2end Memory Network.png)

图(a)中表示的是一个单层的记忆网络，图(b)则表示一个多层的记忆网络

我们先介绍较为简单的图(a)的基本结构，而图(b)则仅仅是图(a)的一个多层版本。

如图(a)中所示，我们输入一系列的句子作为输入，即x1,...,xi作为我们需要记忆的上下文（语境），每一个句子数据输入xi都保存其对应的句向量(sentence embedding)到记忆区域中(mi)，这样我们就得到了关于上下文的记忆表达(memory)，所以简单来说我们仅仅是将我们见过的句子保存到了记忆组件中(history sentences)。

这时，John向模型提出了一个新问句question，同样，我们将其转化为句向量。接下来，我们需要知道的是，对于当前的问题，我们记忆区间中，哪些句子可以用来回答这个问题，或者与这个问题相关？

### 根据输入选择与输入问句相关的记忆

为了达到这个目的，直接采用了类似于注意力模型的方法(attention mechasim)，来计算每一个记忆块与问句的相关程度，其公式如下：

![att.png]({{site.baseurl}}/images/att.png)

其目的在于使用输入的问句向量与每一个记忆块mi计算一个分数，而这个分数就是mi与问句的相关程度，在原文中表达为与问句q的概率相关度，因为根据softmax约束，所有mi的分数相加等于一，符合概率描述的特点。


### 根据相关的记忆输出最终记忆表达

输出的记忆就是相关记忆的权加结果，这个做法非常容易理解，这样与问句越相关的记忆权重就越大，相应的按照不同的比例构成了最后输出的记忆的结果。

### 结合问句与相关记忆进行推理

以上两个步骤其实都是根据输入计算相关的记忆表达，在得到最终的记忆表达之后，就可以根据问句以及记忆进行推理了。这里的推理结果直接使用一个分类结果来进行检验。如果推理正确，应该能够在几个候选的答案中选择出正确的答案。

至此，根据问句选择记忆，并据此进行的推理的全过程就结束了。
整个端到端记忆网络的基本框架就此介绍完毕，下面我们来看看根据这个基本框架我们能够做什么。

## 多层端到端记忆网络 Multiple Layers 

正如我们上面所说，我们的基本框架仅涉及了单层的推理，直接由问句与相关记忆得到一个推理的向量就结束了。而从实际效果和理解上来说，这大概相当于路径为1的搜索逻辑，例如下面这个例子：

John is in the playground.
Bob is in the office.
John picked up the football.
Bob went to the kitchen.

**Where is the football?  **

为了回答where is the football？根据我们上面描述的网络，我们最先得到的推理的输出，应该与John picked up the football有关，因为这与问句有直接联系。而如果我们只有一层推理层，那么很难回答目前football的位置问题。如果再一次加上一个推理层，我们就得到了John is in the playground。这明显有利于我们得到正确答案。

所以往往，我们的推理层数与给出的记忆（事实）有密切关系。

## 如何生成记忆和问句的表达

综上所述，我们整个记忆网络中提到的所有参数依次是：将句子转为为mi的网络参数，根据问句向量计算每一个记忆权重的C网络参数，最后则是根据推理向量进行分类的网络参数。

同时注意到，我们又有很多推理层，如果这些推理层之间共享参数，那么就与递归神经网络RNN之间非常接近了。

而如何根据句子输入生成mi的具体实现，有多种选择例如卷积神经网络（CNN），多层卷积神经网络，递归神经网络（RNN，GRU，LSTM，Bi-directional RNN)。

## Comparing with Native Memory Network

非端到端的记忆网络(Memory Network)其实也并不复杂，与我们上面表述的区别在于，在选择相关fact的时候并不是采用加权和的方法合成得到相应的输出记忆向量，而是直接选取最相关的一个向量来作为输出，如下面公式所示：

![rea.png]({{site.baseurl}}/images/rea.png)

在获得了相应的推理结果之后，第二次推理则基于第一次推理的基础之上，再次搜索最相关的记忆。

![rea2.png]({{site.baseurl}}/images/rea2.png)

虽然从理解上，这样做更加直观，但是带来的不利因素是，这导致整个模型是不可导的，并且必须一步步的根据正确的中间记忆（事实，fact）来一步步的训练网络，这是不现实的。这种做法在注意力模型中叫做硬性注意(hard-attention)，而为了克服整个模型不可导的缺点这里改成使用软性注意(soft-attention)，同时也是端到端记忆网络的最关键创新。 

## Why End to End ？

在讨论端到端之前，可以先介绍一下过去机器学习的系统是如何构建的。最典型的做法是，如果我要做一个系统，它可以拆分成数个子步骤，那么我就必须为这些子步骤构建子系统，为了保证最终的效果，我会分别对这些子系统进行调优，我的想法非常简单，如果我的每一个步骤都是高正确率的，那么我最终的结果也不会太差。这种系统最典型的例子就是统计机器翻译系统（SMT），它通常会包含一个语言模型，一个翻译模型以及一个调序模型。这种做法的弊端在于，对于每一个子系统，我需要单独为它准备训练语料，分别训练，（**而在很多情况下，数据是最大的难题！**）。同时，我的前提假设有可能是不对的，因为从细节上说，最终系统的输出并不是各个子系统简单正确率的乘积，它们不具备简单的链式概率关系。

而端到端的做法，主要得益于神经网络框架的输入，输出以及内部结构非常统一，最末端的结果可以一直反馈到最初的输出层，而不会有太多的阻碍。这样可以很方便的只根据最后结果来训练整套网络。并且根据梯度下降算法，可以得到一个全局最优的解（这取决于你问题的类型，大多数情况下是可以得到全局最优解的）。同时也不再需要为中间层或者中间模块准备单独的数据来进行训练。


## **应用与讨论**

那么这个模型有何用处，宽泛地说，所有带有上下文，依赖于历史，具有较简单的推理的任务都可以使用这个模型，一些NLP中阅读理解的任务最近非常青睐这个模型。

另一些人则关注在它较为独立的大规模记忆模块，由于记忆模块的输出是动态的，同时也可以根据应用分成多种类型，在上面的例子中，就提到了Long Term与Short Term两种类型，牵强的理解可以理解为常识(Long Term)与语境(Short Term)，如果你对神经科学有所了解，你很容易联想到长时记忆与短时记忆。当然，我本人并不很鼓励这么去想，你可以这么理解，但是非要认为这是一回事，那是你的问题。

也因为上面的理由，很容易想到将一个较大规模的知识库进行相应的处理，动态挂在到记忆模块中，这极大的提高了学习的效率。同时也有利于迁移学习，因为很容易想到，加载两个从相似但是不同的任务中学习到的记忆模块来进行训练，这会得到泛化性能更好的结果。

## 代码与实例



## Further Reading

Key-Value Memory Network 

## 参考文献
J. Weston, S. Chopra, and A. Bordes. Memory networks. In International Conference on Learning Representations (ICLR), 2015

Sainbayar Sukhbaatar, Arthur Szlam, Jason Weston, Rob Fergus. End-To-End Memory Networks. Conference on Neural Information Processing Systems (NIPS), 2015
