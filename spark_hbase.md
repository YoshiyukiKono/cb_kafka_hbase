# Spark HBase 

# Spark

```
sudo -u spark spark-shell
```

https://docs.microsoft.com/ja-jp/azure/hdinsight/hdinsight-using-spark-query-hbase

```
scala> import org.apache.spark.sql.execution.datasources.hbase._
<console>:23: error: object hbase is not a member of package org.apache.spark.sql.execution.datasources
       import org.apache.spark.sql.execution.datasources.hbase._
                                                         ^
```

https://docs.cloudera.com/runtime/7.2.1/managing-hbase/topics/hbase-using-hbase-spark-connector.html

https://stackoverflow.com/questions/61834838/hbase-spark-connector-for-cloudera-6-3-2

Sample but the import path does not work
```
import org.apache.spark.sql.DataFrame
import org.apache.spark.sql.execution.datasources.hbase.HBaseTableCatalog
import org.apache.spark.SparkConf
import org.apache.spark.SparkContext
import org.apache.spark.sql.SQLContext

val Patient = spark.read.option("header", "true").csv("/vps_demo/Patient.csv")
Patient.createOrReplaceTempView("Patient_data")
val testDataFrame =
  spark.sql("select REGISTRATIONNO,PATIENTNAME,Age from Patient_data")
testDataFrame.createOrReplaceTempView("testDataFrame")

def catalog = s"""{
    "table":{"namespace":"default","name":"patient900"},
    "rowkey":"key",
    "rowkey":{"cf":"rowkey","col":key","type":"string"},
    "REGISTRATIONNO":{"cf":"personal","col":"REGISTRATIONNO","type":"string"},
    "PATIENTNAME":{"cf":"personal","col":"PATIENTNAME","type":"string"},
    "Age":{"cf":"personal","col":"Age","type":"string"}
    }""".stripMargin

testDataFrame.write
  .options(Map(HBaseTableCatalog.tableCatalog -> catalog))
  .format("org.apache.spark.sql.execution.datasources.hbase")
  .save()
```

Import path that works
```
scala> import org.apache.spark.sql.datasources.hbase.HBaseTableCatalog
import org.apache.spark.sql.datasources.hbase.HBaseTableCatalog
```
