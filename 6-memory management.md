## Spark内存管理
由于drive节点的内存空间较为简单，所以主要分析一下Worker节点的内存管理。首先看一下spark的worker节点的内存分布图：
![](/assets/spark-memory-management.png)


![](/assets/spill to disk.jpg)
![](/assets/evict lru.jpg)

## 参考文献
[1] [Apache Spark 内存管理详解](https://www.ibm.com/developerworks/cn/analytics/library/ba-cn-apache-spark-memory-management/index.html)
[2] [Memory Management in Apache Spark](https://www.slideshare.net/databricks/memory-management-in-apache-spark)
[3] [Distributed Systems Architecture](https://0x0fff.com/spark-memory-management/)