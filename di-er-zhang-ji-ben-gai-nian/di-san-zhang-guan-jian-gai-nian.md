## Job
A parallel computation consisting of multiple tasks that gets spawned in response to a Spark action.**A job is triggered by an action.**
一个Job就是由一个RDD的action动作触发。比如例子中RDD的```foreach()```。SparkContext才会创建一个Job，有DAGScheduler查分成Stage,Stage又被细化成Task，提交给Executor执行。
![](/assets/job.jpg)
## Stage
Each job gets divided into smaller sets of tasks called stages that depend on each other.
从字面意思可以理解stage其实是一些可以在本机执行的task的集合，并且Stage之间是相互依赖的
，一个stage里面的所有Task都是可以不依赖其他机器上的RDD数据独立运行的，比如```map()```,```flatmap()```这样的transformation。
### 1. Stage的划分

[Spark作业调度中stage的划分](https://wongxingjun.github.io/2015/05/25/Spark%E4%BD%9C%E4%B8%9A%E8%B0%83%E5%BA%A6%E4%B8%ADstage%E7%9A%84%E5%88%92%E5%88%86/)
