# spark 历史

## MapReduce原理
![](/assets/mapreduce.jpg)                    
首先，我们一起回顾一下MapReduce的实现原理，上图是WordCount的MR实现原理，图中大致流程可分为：
**Map阶段**
1. 英文句子split为word，输出tuple：&lt; "ctrip",1 &gt; 进行排序
2. 存储到本地磁盘

**Reduce阶段**
1. 对Map阶段的存储在本地的tuple进行全局sort
2. 按照单词合并，推送给每个reduce task
3. 输出结果
***
从中我们能够很明显的看出MapReduce运行慢的主要问题就是I/O，这也是为什么会出现Spark这样一种基于内存的分布式计算模型初衷。
总体看来传统的MapReduce计算模型存在以下不足：
* 磁盘I/O，每次shuffle操作一定会写到磁盘，MR的job前后相互独立；
* 容错能力，在计算MapReduce的任何一个阶段运行失败就会导致整个job失败（Hadoop 0.2.1之前）；
* 查询延迟较高，不能满足adhoc对时效性的要求；
* API接口不够丰富。

## Spark的优势
* 支持内存cache，适合迭代计算；
* 每个MR的task已线程的方式launch；
* 丰富的内置库（Streaming,SQL,MLLib,GraphX）；
* DAG会优化执行过程，减少shuffle；
* 容错能力，根据DAG重启失败的task，RDD生命链恢复丢失的RDD