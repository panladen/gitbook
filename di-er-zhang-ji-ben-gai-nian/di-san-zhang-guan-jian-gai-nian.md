## Job
A parallel computation consisting of multiple tasks that gets spawned in response to a Spark action.**A job is triggered by an action.**
一个Job就是由一个RDD的action动作触发。比如例子中RDD的```foreach()```。ScheduleManager才会创建一个Job并且拆分成Stage,Stage又被细化成Task，提交给Executor执行。

## Stage
