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

Import path that works
```
scala> import org.apache.spark.sql.datasources.hbase.HBaseTableCatalog
import org.apache.spark.sql.datasources.hbase.HBaseTableCatalog
```
