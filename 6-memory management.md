## Spark内存管理
由于drive节点的内存空间较为简单，所以主要分析一下Worker节点的内存管理。首先看一下spark的worker节点的内存分布图：
![内存分布](/assets/spark-memory-management.png "内存分布")

主要包含三个组成部分：
* **Resverved Memory** <br>
这部分内存是Spark预留内存空间，主要目的是为了以后Spark后续版本使用，就目前的Spark版本中不会使用该内存空间用作cache RDD或者进行计算；
* **User Memory** <br>
这部分内存是用户的JVM线程在计算过程中用于存储；
* **Spark Memory** <br>
这部分内存空间由Spark管理，主要用于RDD的持久化和计算，所以分为两个部分：
<ul>
<li>**Storage Memory**<br>
在RDD的```cache()```和```persist(StorageLevel level)```时，保存RDD数据。包括broadcast数据也是存储在该区域。当内存空间不足的时候，Spark会触发spill to disk将数据存储到磁盘中。
</li>
<li>**Execution Memory**<br>
在RDD的计算过程中存储中间结果，比如shuffle intermediate buffer。在内存空间不足的时候，Spark通过触发 evict block to disk存储数据到磁盘。
</li>
![](/assets/spill to disk.jpg)
![](/assets/evict lru.jpg)
</ul>

Property Name|Default|Meaning
----|------|----
spark.memory.fraction | 0.6  | spark管理的内存空间子占比
spark.memory.storageFraction | 0.5 | storage和execution内存非配比例


## 参考文献
[1] [Apache Spark 内存管理详解](https://www.ibm.com/developerworks/cn/analytics/library/ba-cn-apache-spark-memory-management/index.html)
[2] [Memory Management in Apache Spark](https://www.slideshare.net/databricks/memory-management-in-apache-spark)
[3] [Distributed Systems Architecture](https://0x0fff.com/spark-memory-management/)