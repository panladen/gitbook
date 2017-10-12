## Spark SQL

Spark SQL最主要的目的是为了处理结构化数据（structured data），这句话的重点就是Spark SQL处理的每条数据都是含有结构信息（schema info），并且Spark SQL支持使用SQL语句去处理结构化数据。

### 1. Datasets和DataFrames
首先明确一点，Datasets和DataFrames在Spark 2.0之后的版本中是同一个概念。Datasets和DataFrame数据底层的实现还是RDD，只不过是在RDD的基础上加上了数据结构的描述信息。