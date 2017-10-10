## Spark运行模式
spark目前支持的运行模式有：local,standalone,cluster，可以通过在创建**SparkSession**或者**SparkContext**的时候通过```master()```函数指定。
* local模式：主要用在IDE中进行开发和调试用
* standalone：单机模式，适合集群规模较小的job
* cluster模式：集群模式（YARN，Mesos）
![cluster模式](/assets/cluster-overview.png "cluster模式")

## Cluster运行模式的具体实现
### 1. 物理部署的组成部分

* **_Driver Program_**: Spark应用的主函数，是spark应用的master，其主要作用：<br>
1) 创建SparkContext;<br>
2) 向**Cluster Manager**申请资源<br>
3) 将一个Spark应用转换成DAG，调度和管理Task的运行<br>
4) 接收Executor的处理结果，比如rdd的```collect()```
* **_Cluster Manager_**: 集群资源管理器，如YARN,Mesos等。
* **_Executor_**: Spark负责运行spark应用的task的JVM进程。

> **注：** 如果executor返回的数据结果超过driver机器可申请的最大内存空间就会导致oom问题

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






