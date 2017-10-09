# spark 历史

![](/assets/mapreduce.jpg)                    
首先，我们一起回顾一下MapReduce的实现原理，上图是WordCount的MR实现原理，图中大致流程可分为：
**Map阶段**
1. 英文句子split为word，输出tuple：&lt; "ctrip",1 &gt; 进行排序
2. 存储到本地磁盘

**Reduce阶段**
1. 对Map阶段的存储在本地的tuple进行全局sort
2. 按照单次合并，推送给每个reduce task
3. 输出结果