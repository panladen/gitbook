## 1. RDD
RDD(Resilient Distributed Dataset)弹性分布式数据集，是Spark中的抽象数据结构类型。任何数据在Spark中都被表示为RDD，从编程角度来看，RDD可以简单的看成一个分布式数组。所以Spark应用可以被看成是，把需要处理的数据转换成RDD，然后对RDD进行一系列的变换和操作从而得到结果。
![](/assets/Spark.png)
## 2. 