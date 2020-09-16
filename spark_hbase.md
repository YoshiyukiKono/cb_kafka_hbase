# Spark HBase

# HBase

## Test Table

https://docs.microsoft.com/ja-jp/azure/hdinsight/hdinsight-using-spark-query-hbase

```
hbase shell

```

```
create 'Contacts', 'Personal', 'Office'
```

```
put 'Contacts', '1000', 'Personal:Name', 'John Dole'
put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
```
```
hbase(main):013:0> list
TABLE                                                                                                                                                                  
Contacts                                                                                                                                                               
1 row(s)
Took 0.0166 seconds                                                                                                                                                    
=> ["Contacts"]
hbase(main):014:0> describe 'Contacts'
Table Contacts is ENABLED                                                                                                                                              
Contacts                                                                                                                                                               
COLUMN FAMILIES DESCRIPTION                                                                                                                                            
{NAME => 'Office', VERSIONS => '1', EVICT_BLOCKS_ON_CLOSE => 'false', NEW_VERSION_BEHAVIOR => 'false', KEEP_DELETED_CELLS => 'FALSE', CACHE_DATA_ON_WRITE => 'false', D
ATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', MIN_VERSIONS => '0', REPLICATION_SCOPE => '0', BLOOMFILTER => 'ROW', CACHE_INDEX_ON_WRITE => 'false', IN_MEMORY => 'fal
se', CACHE_BLOOMS_ON_WRITE => 'false', PREFETCH_BLOCKS_ON_OPEN => 'false', COMPRESSION => 'NONE', BLOCKCACHE => 'true', BLOCKSIZE => '65536'}                          
{NAME => 'Personal', VERSIONS => '1', EVICT_BLOCKS_ON_CLOSE => 'false', NEW_VERSION_BEHAVIOR => 'false', KEEP_DELETED_CELLS => 'FALSE', CACHE_DATA_ON_WRITE => 'false',
 DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', MIN_VERSIONS => '0', REPLICATION_SCOPE => '0', BLOOMFILTER => 'ROW', CACHE_INDEX_ON_WRITE => 'false', IN_MEMORY => 'f
alse', CACHE_BLOOMS_ON_WRITE => 'false', PREFETCH_BLOCKS_ON_OPEN => 'false', COMPRESSION => 'NONE', BLOCKCACHE => 'true', BLOCKSIZE => '65536'}                        
2 row(s)
Took 0.0711 seconds                                                                                                                                                                                                                                                                                                 
hbase(main):016:0> get 'Contacts', '1000', 'Personal'
COLUMN                                     CELL                                                                                                                        
 Personal:Name                             timestamp=1600232442490, value=John Dole                                                                                    
 Personal:Phone                            timestamp=1600232453090, value=1-425-000-0001                                                                               
1 row(s)
Took 0.5829 seconds                                                                                                                                                    
hbase(main):017:0> get 'Contacts', '1000', 'Office'
COLUMN                                     CELL                                                                                                                        
 Office:Address                            timestamp=1600232453476, value=1111 San Gabriel Dr.                                                                         
 Office:Phone                              timestamp=1600232453254, value=1-425-000-0002                                                                               
1 row(s)
Took 0.1204 seconds                                                                                                                                                    
hbase(main):018:0> 
```

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

## Import path that works

```
sudo -u spark spark-shell --jars /opt/cloudera/parcels/CDH/lib/hbase/hbase-spark.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-spark-it.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-client.jar
```
Important: No 'execution.'
```
scala> import org.apache.spark.sql.datasources.hbase.HBaseTableCatalog
import org.apache.spark.sql.datasources.hbase.HBaseTableCatalog
```

```
import org.apache.spark.sql.{SQLContext, _}
import org.apache.spark.sql.datasources.hbase._
import org.apache.spark.{SparkConf, SparkContext}
import spark.sqlContext.implicits._
```

```
def catalog = s"""{
    |"table":{"namespace":"default", "name":"Contacts"},
    |"rowkey":"key",
    |"columns":{
    |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
    |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
    |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
    |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
    |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
    |}
|}""".stripMargin
```

```
def withCatalog(cat: String): DataFrame = {
    spark.sqlContext
    .read
    .options(Map(HBaseTableCatalog.tableCatalog->cat))
    .format("org.apache.hadoop.hbase.spark")
    .load()
 }
```

```
scala> val df = withCatalog(catalog)
java.lang.NoClassDefFoundError: org/apache/hadoop/hbase/client/TableDescriptor
  at org.apache.hadoop.hbase.spark.DefaultSource.createRelation(DefaultSource.scala:70)
  at org.apache.spark.sql.execution.datasources.DataSource.resolveRelation(DataSource.scala:317)
  at org.apache.spark.sql.DataFrameReader.loadV1Source(DataFrameReader.scala:223)
  at org.apache.spark.sql.DataFrameReader.load(DataFrameReader.scala:211)
  at org.apache.spark.sql.DataFrameReader.load(DataFrameReader.scala:167)
  at withCatalog(<console>:43)
  ... 55 elided
Caused by: java.lang.ClassNotFoundException: org.apache.hadoop.hbase.client.TableDescriptor
  at java.net.URLClassLoader.findClass(URLClassLoader.java:382)
  at java.lang.ClassLoader.loadClass(ClassLoader.java:418)
  at java.lang.ClassLoader.loadClass(ClassLoader.java:351)
  ... 61 more

```
https://stackoverflow.com/questions/56364850/i-save-a-dataframe-in-hbase-and-i-get-java-lang-noclassdeffounderror-org-apach

```
scala> val df = withCatalog(catalog)
java.lang.NullPointerException
  at org.apache.hadoop.hbase.spark.HBaseRelation.<init>(DefaultSource.scala:139)
  at org.apache.hadoop.hbase.spark.DefaultSource.createRelation(DefaultSource.scala:70)
  at org.apache.spark.sql.execution.datasources.DataSource.resolveRelation(DataSource.scala:317)
  at org.apache.spark.sql.DataFrameReader.loadV1Source(DataFrameReader.scala:223)
  at org.apache.spark.sql.DataFrameReader.load(DataFrameReader.scala:211)
  at org.apache.spark.sql.DataFrameReader.load(DataFrameReader.scala:167)
  at withCatalog(<console>:38)
  ... 55 elided

```

https://stackoverflow.com/questions/52372245/hbase-spark-load-data-raise-nullpointerexception-error-scala

```
sudo -u spark spark-shell --jars /opt/cloudera/parcels/CDH/lib/hbase/hbase-spark.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-spark-it.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-client.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-common.jar
```

```
import org.apache.hadoop.hbase.HBaseConfiguration
import org.apache.hadoop.hbase.spark.HBaseContext
import org.apache.hadoop.fs.Path

val conf = HBaseConfiguration.create()
```

```
sudo -u spark spark-shell --jars /opt/cloudera/parcels/CDH/lib/hbase/hbase-spark.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-spark-it.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-client.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-common.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-server.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-server.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-mapreduce.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-shaded-miscellaneous.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-protocol.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-protocol-shaded.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-shaded-protobuf.jar,/opt/cloudera/parcels/CDH/lib/hbase/hbase-shaded-netty.jar
```

```
import org.apache.hadoop.hbase.HBaseConfiguration
import org.apache.hadoop.hbase.spark.HBaseContext
import org.apache.hadoop.fs.Path

import org.apache.spark.sql.{SQLContext, _}
import org.apache.spark.sql.datasources.hbase._
import org.apache.spark.{SparkConf, SparkContext}
import spark.sqlContext.implicits._

val conf = HBaseConfiguration.create()
conf.addResource(new Path("/etc/hbase/conf.cloudera.hbase/hbase-site.xml"))

new HBaseContext(sc, conf)
```


```
def catalog = s"""{
    |"table":{"namespace":"default", "name":"Contacts"},
    |"rowkey":"key",
    |"columns":{
    |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
    |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
    |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
    |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
    |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
    |}
|}""".stripMargin

def withCatalog(cat: String): DataFrame = {
    spark.sqlContext
    .read
    .options(Map(HBaseTableCatalog.tableCatalog->cat))
    .format("org.apache.hadoop.hbase.spark")
    .load()
 }
```

```
val df = withCatalog(catalog)
df.show()
```
```
+------+--------------+--------------------+------------+--------------+        
|rowkey|   officePhone|       officeAddress|personalName| personalPhone|
+------+--------------+--------------------+------------+--------------+
|  1000|1-425-000-0002|1111 San Gabriel Dr.|   John Dole|1-425-000-0001|
|  8396|  230-555-0191|5415 San Gabriel Dr.| Calvin Raji|  230-555-0191|
+------+--------------+--------------------+------------+--------------+
```

### Spark SQL
```
scala> df.createTempView("contacts")

scala> spark.sqlContext.sql("select personalName, officeAddress from contacts").show
+------------+--------------------+
|personalName|       officeAddress|
+------------+--------------------+
|   John Dole|1111 San Gabriel Dr.|
| Calvin Raji|5415 San Gabriel Dr.|
+------------+--------------------+
```

### Insert New Line

```
case class ContactRecord(
    rowkey: String,
    officeAddress: String,
    officePhone: String,
    personalName: String,
    personalPhone: String
    )
val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

var newData = new Array[ContactRecord](1)
newData(0) = newContact

sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.hadoop.hbase.spark").save()

```

```
scala> df.show()
+------+--------------+--------------------+------------+--------------+
|rowkey|   officePhone|       officeAddress|personalName| personalPhone|
+------+--------------+--------------------+------------+--------------+
|  1000|1-425-000-0002|1111 San Gabriel Dr.|   John Dole|1-425-000-0001|
| 16891|  674-555-0110|        40 Ellis St.|John Jackson|  230-555-0194|
|  8396|  230-555-0191|5415 San Gabriel Dr.| Calvin Raji|  230-555-0191|
+------+--------------+--------------------+------------+--------------+
```

#### Check on HBase (Shell)

```
hbase(main):018:0> get 'Contacts', '16891'
COLUMN                                     CELL                                                                                                                        
 Office:Address                            timestamp=1600239454960, value=40 Ellis St.                                                                                 
 Office:Phone                              timestamp=1600239454960, value=674-555-0110                                                                                 
 Personal:Name                             timestamp=1600239454960, value=John Jackson                                                                                 
 Personal:Phone                            timestamp=1600239454960, value=230-555-0194                                                                                 
1 row(s)
Took 0.0247 seconds                                                                                                                                                    
hbase(main):019:0> 
```
