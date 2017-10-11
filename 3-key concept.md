## 1. Job
A parallel computation consisting of multiple tasks that gets spawned in response to a Spark action.**A job is triggered by an action.**
一个Job就是由一个RDD的action动作触发。比如例子中RDD的```foreach()```。SparkContext才会创建一个Job，有DAGScheduler查分成Stage,Stage又被细化成Task，提交给Executor执行。

![](/assets/job.jpg)

## 2. Stage
Each job gets divided into smaller sets of tasks called stages that depend on each other.
从字面意思可以理解stage其实是一些可以在本机执行的task的集合，并且Stage之间是相互依赖的
，一个stage里面的所有Task都是可以不依赖其他机器上的RDD数据独立运行的，比如```map()```,```flatmap()```这样的transformation。

### 2.1 Stage的划分（来自网络）
stage的划分是Spark作业调度的关键一步，它基于DAG确定依赖关系，借此来划分stage，将依赖链断开，每个stage内部可以并行运行，整个作业按照stage顺序依次执行，最终完成整个Job。实际应用提交的Job中RDD依赖关系是十分复杂的，依据这些依赖关系来划分stage自然是十分困难的，Spark此时就利用了前文提到的依赖关系，调度器从DAG图末端出发，逆向遍历整个依赖关系链，遇到ShuffleDependency（宽依赖关系的一种叫法）就断开，遇到NarrowDependency就将其加入到当前stage。stage中task数目由stage末端的RDD分区个数来决定，RDD转换是基于分区的一种粗粒度计算，一个stage执行的结果就是这几个分区构成的RDD。[Spark作业调度中stage的划分](https://wongxingjun.github.io/2015/05/25/Spark%E4%BD%9C%E4%B8%9A%E8%B0%83%E5%BA%A6%E4%B8%ADstage%E7%9A%84%E5%88%92%E5%88%86/)

![](/assets/stageDivide.jpg)

其中涉及两个重要概念：
* 窄依赖: 父RDD的分区最多只会被子RDD的一个分区使用，比如map、filter、union
* 宽依赖: 父RDD的一个分区会被子RDD的多个分区使用，比如groupByKey、join

### 2.2 stage分类 （来自网络）
* **ShuffleMapStage**，shuffle之前是一个stage，shuffle之后的操作是另一个stage
* **ResultStage**，RDD的action中并不需要shuffle，直接输出（show,foreach）创建stage

## 4. SparkContext
SparkContext作为Spark应用的入口，能够连接spark应用和集群。通过SparkContext能够创建RDD、累加器（accumulator）、广播全局变量（broadcast variables）。在Spark 2.0之前的版本所有的spark应用都必须首先创建**SparkContext**，**SQLContext**或者**HiveContext**是Spark SQL的入口，**StreamingContext**是Spark Streaming的入口。在Spark 2.X版本中引入```SparkSeesion```，SparkSession是基于SparkContext的一种更为高级的抽象，其包含了SQLContext、HiveContext和StreamingContext的功能。

## 3. Task
A unit of work that will be sent to one executor.
Task是数据处理的真正执行单元，会被分配给executor去执行。一个RDD有多少个partition就会有多少个task。每个Task只会处理一个partition上面的数据。