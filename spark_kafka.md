# Spark Kafka

https://www.amazon.co.jp/-/en/%E6%A0%AA%E5%BC%8F%E4%BC%9A%E7%A4%BENTT%E3%83%87%E3%83%BC%E3%82%BF-ebook/dp/B07H8D6CL9/ref=sr_1_1?dchild=1&keywords=Apache+kafka&qid=1600242224&sr=8-1

```
scala> val reader = spark.readStream
reader: org.apache.spark.sql.streaming.DataStreamReader = org.apache.spark.sql.streaming.DataStreamReader@37263abc

scala> val formatConfigured = reader.format("kafka")
formatConfigured: org.apache.spark.sql.streaming.DataStreamReader = org.apache.spark.sql.streaming.DataStreamReader@37263abc
```
no additional jar is used.

```
scala> val optionConfigured = formatConfigured.option("subscribe","test-default").option("kafka.bootstrap.servers","ip-10-0-0-249.us-east-2.compute.internal")
optionConfigured: org.apache.spark.sql.streaming.DataStreamReader = org.apache.spark.sql.streaming.DataStreamReader@37263abc
```

```
scala> testDS.printSchema
root
 |-- key: binary (nullable = true)
 |-- value: binary (nullable = true)
 |-- topic: string (nullable = true)
 |-- partition: integer (nullable = true)
 |-- offset: long (nullable = true)
 |-- timestamp: timestamp (nullable = true)
 |-- timestampType: integer (nullable = true)
```

```
scala> val selectedDS = testDS.selectExpr("CAST(value AS string) AS value_as_str")
selectedDS: org.apache.spark.sql.DataFrame = [value_as_str: string]

scala> selectedDS.printSchema
root
 |-- value_as_str: string (nullable = true)


scala> selectedDS.show
org.apache.spark.sql.AnalysisException: Queries with streaming sources must be executed with writeStream.start();;
```

```
scala> val writer = selectedDS.writeStream
writer: org.apache.spark.sql.streaming.DataStreamWriter[org.apache.spark.sql.Row] = org.apache.spark.sql.streaming.DataStreamWriter@2ab53df6
```

Spark Streaming Consumer Java Sample

https://www.sbcloud.co.jp/entry/2019/07/03/kafka_1
