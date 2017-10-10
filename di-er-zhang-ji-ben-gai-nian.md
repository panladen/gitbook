## 运行模式

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







