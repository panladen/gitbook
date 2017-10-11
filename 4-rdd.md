## 1. RDD
RDD(Resilient Distributed Dataset)弹性分布式数据集，是Spark中的抽象数据结构类型。任何数据在Spark中都被表示为RDD，从编程角度来看，RDD可以简单的看成一个分布式数组。所以Spark应用可以被看成是，把需要处理的数据转换成RDD，然后对RDD进行一系列的变换和操作从而得到结果。
![](/assets/Spark.png)
## 2. RDD的创建
### 2.1 Parallelized collection
从一个collection转换过来，这个通常用在比较小的数组转换成RDD。例如：
``` java
List<Integer> data = Arrays.asList(1, 2, 3, 4, 5);
new JavaSparkContext(sparkSession.sparkContext()).parallelize(data);
```

### 2.2 从外部数据集加载
Spark支持的外部数据集有本地文件、HDFS、HBase、JDBC等等。可以通过```SparkSession.read()```函数获取```DataFrameReader```实例，从而获取非常丰富的文件记载API。

* json文件中加载RDD
``` java
sparkSession.read().json("path/of/json/file").rdd();
```

* hive的orc文件中加载RDD
``` java
sparkSession.read().orc("path/of/orc/file").rdd();
```

> **注** textFile()加载本地文件的时候，必须保证Drive节点和Work节点的相同目录下面包含文件

