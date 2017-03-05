---
published: false
---

## What is Symbolic System? 


## how to build graph?


## how to backward? automatic?



## extend memory as a history status keeper 
例如：NTM
神经图灵机粗略的可以看成是一个LSTM的增强版本，在LSTM中不同的gate控制了从过去的状态中应该学习什么内容。但是，即使是这样过去的状态也仅能表现成一个向量 (recall the gate controlling vector)， 与之不同的是，NTM将其增加成一个矩阵M,这样在获取目前输入与过去状态的关系的时候，就不用仅仅局限在一个简单的vector中，这样M的大小直接影响了网络能够记住过去信息的能力。而过去的RNN（或者LSTM）则相当于一个只有一个slot的memory。

但是值得注意的是NTM与LSTM还是有很多不同的，尽管现在普遍认为NTM比LSTM效果要好（在某些任务上）。
NTM中的memory的行为与LSTM中的门控类似，与GRU中的门控也类似，不具备太多显式的意思(explicit meaning)，其主要随着end to end training 进行更新。

read from memory and write to the memory
从记忆中的读操作主要依赖于attention mechaism

## memory as explicit context keeper 
例如： memory network end2end
记忆网络的观点与NTM在不同层次上不尽相同，NTM是针对序列数据的低级建模，通过不断地计算当前输入与记忆之间的关系，生成一个序列的隐状态，这适用于序列生成的任务，这样的任务很容易想到是基于词的输入或者甚至是基于字母级别的输入。而memory network针对的问答或者对话任务明显不能简单的依靠词或者字母来完成，通常的做法是将句子作为基本单位，并且关注一系列句子之间的关系，这样的句子序列，由于每一个句子都包含了明确的信息（是否包含直接影响推理的关键词），并且在任务中，通常也显式提供了这样的上下文，memory network采取的办法则是，将这些句子直接转化为句向量，并存储到memory slot中，类似这样的任务，由于上下文给定，在存储到memory的过程中，不可避免的要处理不等长的记忆的问题，例如：设定memory大小为50长度，每一个memory slot规模为150的情况下，上下文为10个句子的与70个句子的单个问题样本，就必须要处理如何补齐memory（目前一般使用mask）以及如何挑选有用的上下文的情况（选用最近的50句或者最相关的50句的问题）。这样在多次的推理的过程，通过不断地对记忆模块进行读取，一步步的推理得到答案。


## memory as implicit context keeper
与memory network的记忆机制有所不同，IRN(Implicit Reasoner Network)的记忆结构认为，如果将句子上下文显式保存进memory component那么除了要关注上下文与当前问句之间的关系之外，更重要的是，memory很有可能不能很好的记住task之间最关键的模式。IRN将NTM类似的memory引入了推理，强调的是在hop之间的模糊推理。但是同样导致了这些推理之间可能很难解释其关系。



## memory to store the samples : one shot learning 
memory可以用于记忆我们关系的状态，上下文，那么同样也可以同于记忆样本(参考matching network: one-shot learning)，其memory中记忆的是与样本直接相关的信息。





## Further Reading

Key-Value Memory Network 

## 参考文献
J. Weston, S. Chopra, and A. Bordes. Memory networks. In International Conference on Learning Representations (ICLR), 2015

Sainbayar Sukhbaatar, Arthur Szlam, Jason Weston, Rob Fergus. End-To-End Memory Networks. Conference on Neural Information Processing Systems (NIPS), 2015

