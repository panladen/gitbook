# spark 历史

![](/assets/mapreduce.jpg)                    
首先，我们一起回顾一下MapReduce的实现原理，上图是WordCount的MR实现原理，图中大致流程可分为：
#### Map节点
1. 英文句子split为word，排序
2. 存储到本地磁盘