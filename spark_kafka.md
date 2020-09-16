# Spark Kafka

```
scala> val reader = spark.readStream
reader: org.apache.spark.sql.streaming.DataStreamReader = org.apache.spark.sql.streaming.DataStreamReader@37263abc

scala> val formatConfigured = reader.format("kafka")
formatConfigured: org.apache.spark.sql.streaming.DataStreamReader = org.apache.spark.sql.streaming.DataStreamReader@37263abc
```
no additional jar is used.

