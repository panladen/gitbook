## Spark运行模式
spark目前支持的运行模式有：local,standalone,cluster，可以通过在创建**SparkSession**或者**SparkContext**的时候通过```master()```函数指定。
* local模式：主要用在IDE中进行开发和调试用
* standalone：单机模式，适合集群规模较小的job
* cluster模式：集群模式（YARN，Mesos）

![cluster模式](/assets/cluster-overview.png "cluster模式")

## Cluster运行模式的具体实现
### 1. 物理节点的组成

* **_Driver Program_**: Spark应用的主函数，是spark应用的master，其主要作用：<br>
1) 创建SparkContext;<br>
2) 向**Cluster Manager**申请资源<br>
3) 将一个Spark应用转换成DAG，调度和管理Task的运行<br>
4) 接收Executor的处理结果，比如rdd的```collect()```
* **_Cluster Manager_**: 集群资源管理器，如YARN,Mesos等，主要负责资源管理、job运行及监控
* **_Executor_**: Spark负责运行spark应用的task的JVM进程。

> **注：** 如果executor返回的数据结果超过driver机器可申请的最大内存空间就会导致oom问题

### 2. 运行流程
结合WordCount，分析一下一个Spark应用的具体运行流程，代码如下：
``` java
public static void main(String[] args) throws IOException {
        SparkSession sparkSession = SparkSession.builder()
                .appName("SparkSQL")
                .master("local")
                .getOrCreate();
        RDD lines = sparkSession.sparkContext().textFile("src\\main\\resources\\data.txt",1);
        
        lines.toJavaRDD().flatMapToPair((PairFlatMapFunction) line -> {
            if(line == null) return null;
            List<Tuple2> result = new ArrayList<>();
            String lineStr = String.valueOf(line);
            String[] words = lineStr.split(" ");
            for(String word : words){
                result.add(new Tuple2(word,1));
            }
            return result.iterator();
        }).reduceByKey((Function2) (v1, v2) -> {
            Integer c1 = (Integer) v1;
            Integer c2 = (Integer) v2;
            return c1 + c2;
        }).foreach((VoidFunction) tuple -> System.out.println(tuple.toString()));
}
```
第一行代码：创建SparkSession/SparkContext，并且指明运行模式为local
``` java
SparkSession sparkSession = SparkSession.builder()
                .appName("SparkSQL")
                .master("local")
                .getOrCreate();
``` 
第二行代码：从文件中加载数据，创建RDD
``` java
RDD lines = sparkSession.sparkContext().textFile("src\\main\\resources\\data.txt",1);
```
第三行代码：通过```flatMapToPair```生成PairRDD，然后使用```reduceByKey```对单词进行count计算。SparkContext根据RDD的Transformation和Action动作之间的关系创建DAG，如下图所示：

![](/assets/DAG.jpg)

``` java
lines.toJavaRDD().flatMapToPair((PairFlatMapFunction) line -> {
    if(line == null) return null;
    List<Tuple2> result = new ArrayList<>();
    String lineStr = String.valueOf(line);
    String[] words = lineStr.split(" ");
    for(String word : words){
        result.add(new Tuple2(word,1));
    }
    return result.iterator();
}).reduceByKey((Function2) (v1, v2) -> {
    Integer c1 = (Integer) v1;
    Integer c2 = (Integer) v2;
    return c1 + c2;
}).foreach((VoidFunction) tuple -> System.out.println(tuple.toString()));
```
大致流程如下：
* 由于Spark RDD的运行模式为[Lazy Evaluation](http://data-flair.training/blogs/apache-spark-lazy-evaluation/),一直到SparkContext遇到RDD的action操作时候才会将DAG提交给DAGScheduler。
* DAGSchedule会根据一定的规则（宽依赖，窄依赖）将DAG拆分成若干个Stage，并提交给TaskScheduler
* TaskScheduler会调度这些Task在集群上运行，并且监控其运行状态。

