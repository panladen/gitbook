## Spark SQL

Spark SQL最主要的目的是为了处理结构化数据（structured data），这句话的重点就是Spark SQL处理的每条数据都是含有结构信息（schema info），并且Spark SQL支持使用SQL语句去处理结构化数据。

### 1. Datasets和DataFrames
首先明确一点，Datasets和DataFrames在Spark 2.0之后的版本中是同一个概念。Datasets和DataFrame数据底层的实现还是RDD，只不过是在RDD的基础上加上了数据结构的描述信息。

### 2. DataFrame的创建
#### 2.1 从RDD中转换而来
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

#### 2.2 从文件中加载
#### 2.3 从DataSource中读取数据
