---
layout: post
title: Memory Network End2End
published: true
---

## Memory Network End2End

在介绍**端到端记忆网络**(End to End Memory Network)之前，我想先介绍一下这个技术为什么会出现，以及该技术是为了解决怎样的问题。

从技术路线来说，**端到端记忆网络**来自于**记忆网络**( J. Weston, S. Chopra, and A. Bordes. Memory networks. In International Conference on Learning Representations (ICLR), 2015)。准确地说，端到端记忆网络是记忆网络的一个改良版本。记忆网络的出现主要是为了解决一些依赖于（Long Term/Short Term,长期以及短期）记忆内容依赖的应用。

比如，在对话应用中，John与对话机器人Robt进行交谈，John的第n轮发言，显然与前n-1轮的对话内容有一定的联系（请注意这是一个假设），理由非常简单，在前n-1轮对话中，John显式或者隐式的提供了关于自己的信息，这些信息包括了他交谈的目的，他自身的兴趣，他提出的补充条件等（aim, interest, condition）。如下图所示：


## Memory Network
记忆网络是Facebook AI Research提出的一个主要应用在Question Answersing(Q&A)任务中的网络，其原理比较简单明了，





![End2End Memory Network]({{site.baseurl}}/_posts/end2end Memory Network.png)




