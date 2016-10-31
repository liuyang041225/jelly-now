---
layout: post
title: Memory Network End2End
published: true
---

## Memory Network End2End

在介绍**端到端记忆网络**(End to End Memory Network)之前，我想先介绍一下这个技术为什么会出现，以及该技术是为了解决怎样的问题。

从技术路线来说，**端到端记忆网络**来自于**记忆网络**( J. Weston, S. Chopra, and A. Bordes. Memory networks. In International Conference on Learning Representations (ICLR), 2015)。准确地说，端到端记忆网络是记忆网络的一个改良版本。记忆网络的出现主要是为了解决一些依赖于（Long Term/Short Term,长期以及短期）记忆内容依赖的应用。

比如，在对话应用中，John与对话机器人Robt进行交谈，John的第n轮发言，显然与前n-1轮的对话内容有一定的联系（请注意这是一个假设），理由非常简单，在前n-1轮对话中，John显式或者隐式的提供了关于自己的信息，这些信息包括了他交谈的目的，他自身的兴趣，他提出的补充条件等（aim, interest, condition）。如下图所示：
![Example]({{site.baseurl}}/_posts/dialog.png)

* John与Robt的对话发生在上图提示的Short-Term Memories中，在第1轮的交流中，我们知道John想要找一部电影来打发晚上的时光(c)，系统回应(response)则推荐电影Shaolin Soccer给John。
* John回复Shaolin Soccer以及Kung Fu Hustle他已经看过，他想要看更多Stephen Chow的电影。
* **系统的任务就是在已知上面事实已经发生的情况下，返回给John一个比较合理的答案。**

如何实现具备这种功能的模型？这显然有别于我们在之前的内容中讨论的各种神经网络（CNN,LSTM,GRU，etc），就从原理上来说，**这些深度网络结构提供的主要是从数据中学习到更好表达的功能**(Learning better represetation from data)，而显然我们需要的是具备寻找相关事实，并进行基本推理的模型。记忆网络(Memory Networks)便提供了这些功能，因为：

* 记忆网络具有一个记忆组件可以对记忆进行读、写操作 (Class of Model that combine large memory with learning component that can **read** and **write** to it)
* 记忆网络实现了对记忆组件中内容的选择性关注，并基于这些选择性关注的内容进行推理 (Incorporates **reasoning** with **attention** over **memory** (RAM))
* 大多数机器学习算法具有一个有限的记忆，而这些记忆对一些低级的任务实际上已经足够。(参考，RNN系列模型的长短时记忆对分类的影响)

## Basic Framework of End to End Memory Network

基于上面的描述，其实并不难想象记忆网络的基本结构,如下图所示：
![End2End Memory Network]({{site.baseurl}}/_posts/end2end Memory Network.png)

图(a)中表示的是一个单层的记忆网络，图(b)则表示一个多层的记忆网络

我们先介绍较为简单的图(a)的基本结构，而图(b)则仅仅是图(a)的一个多层版本。

如图(a)中所示，我们输入一系列的句子作为输入，即x1,...,xi作为我们需要记忆的上下文（语境），每一个句子数据输入xi都保存其对应的句向量(sentence embedding)到记忆区域中(mi)，这样我们就得到了关于上下文的记忆表达(memory)，所以简单来说我们仅仅是将我们见过的句子保存到了记忆组件中(history sentences)。

这时，John向模型提出了一个新问句question，同样，我们将其转化为句向量。接下来，我们需要知道的是，对于当前的问题，我们记忆区间中，哪些句子可以用来回答这个问题，或者与这个问题相关？

1.根据输入选择与输入问句相关的记忆

为了达到这个目的，直接采用了类似于注意力模型的方法(attention mechasim)，来计算每一个记忆块与问句的相关程度，其公式如下：
![att.png]({{site.baseurl}}/_posts/att.png)

其目的在于使用输入的问句向量与每一个记忆块mi计算一个分数，而这个分数就是mi与问句的相关程度，在原文中表达为与问句q的概率相关度，因为根据softmax约束，所有mi的分数相加等于一，符合概率描述的特点。


2.根据相关的记忆输出最终记忆表达

输出的记忆就是相关记忆的权加结果，这个做法非常容易理解，这样与问句越相关的记忆权重就越大，相应的按照不同的比例构成了最后输出的记忆的结果。

3.结合问句与相关记忆进行推理

以上两个步骤其实都是根据输入计算相关的记忆表达，在得到最终的记忆表达之后，就可以根据问句以及记忆进行推理了。这里的推理结果直接使用一个分类结果来进行检验。如果推理正确，应该能够在几个候选的答案中选择出正确的答案。

至此，根据问句选择记忆，并据此进行的推理的全过程就结束了。



记忆网络是Facebook AI Research提出的一个主要应用在Question Answersing(Q&A)任务中的网络，其原理比较简单明了，
