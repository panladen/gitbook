## Spark SQL

Spark SQL最主要的目的是为了处理结构化数据（structured data），这句话的重点就是Spark SQL处理的每条数据都是含有结构信息（schema info），并且Spark SQL支持使用SQL语句去处理结构化数据。

### 1. Datasets和DataFrames
首先明确一点，Datasets和DataFrames在Spark 2.0之后的版本中是同一个概念。Datasets和DataFrame数据底层的实现还是RDD，只不过是在RDD的基础上加上了数据结构的描述信息。

> **注** DataFrame/Dataset的操作，Spark会使用Catalyst进行优化，性能方面会比RDD要高一些，详情见[性能优势](https://databricks.com/blog/2016/07/14/a-tale-of-three-apache-spark-apis-rdds-dataframes-and-datasets.html)。
![](/assets/filter-down.png)
上图显示了两张表join后filter的优化，从中能够看出先filter再Join的执行效率更高。

### 2. DataFrame的创建和存储
#### 2.1 DataFrame的创建
##### 2.1.1 从RDD中转换而来
* 利用反射，指定

``` java
List<StatisticResult> statisticList = Arrays.asList(
        new StatisticResult("00001","baidu"),
        new StatisticResult("00002", "jinritoutiao"),
        new StatisticResult("00003", "jinritoutiao")
);
sparkSession.createDataFrame(javaSparkcontext.parallelize(statisticList), StatisticResult.class)
        .registerTempTable("statisicTab");
sparkSession.sql("SELECT collectID,source FROM statisicTab").show();
```

* 指定Schema

SqlContext的```applySchema```分别支持用StructType和Class来指定，我感觉用class指定更加适用。

```java
sparkSession.sqlContext().applySchema(javaSparkcontext.parallelize(statisticList),StatisticResult.class);
```

##### 2.1.2 从文件中加载
可以使用```sparkSession.read()```获取DataFrameReader读取结构数据文件（json, parquet, jdbc, orc, libsvm, csv, text等）。

##### 2.1.3 从DataSource中读取数据
使用```HiveContext```读取hive中的数据，或者可以使用```SparkContext.newAPIHadoopRDD()```读取HBase或其他Hadoop数据。

> **注** HiveContext是Spark本身内置的，会在drive根据当前环境变量和hive-site.xml相关信息进行初始化

#### 2.2 DataFrame的存储
##### 2.2.1 存储到Hive表中
DataFrame可以通过```DataFrameWriter.saveAsTable(String tableName)```保存到Hive表中（表不存在会自动创建表），还可以使用```DataFrameWriter.partitionBy```指明分区字段。

#### 2.2.2 存储到其他文件格式中
通过```DataFrameWriter.format()```指定保存的文件格式，可以将DataFrame保存到txt,json,orc等文件中。