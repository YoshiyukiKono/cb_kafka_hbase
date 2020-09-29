# Kafka Spark-Streaming

https://github.com/trK54Ylmz/kafka-spark-streaming-example


## Maven

```
curl -OL https://downloads.apache.org/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
tar -zxvf apache-maven-3.6.3-bin.tar.gz
sudo mv apache-maven-3.6.3 /opt/
cd /opt
sudo su
ln -s /opt/apache-maven-3.6.3/ apache-maven
```
https://qiita.com/Esfahan/items/60cf425514c66553bd42
```
dirname $(readlink $(readlink $(which java)))
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.262.b10-0.el7_8.x86_64/jre/bin
sudo vi /etc/profile
```

```
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.262.b10-0.el7_8.x86_64
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar 
export PATH=$PATH:/opt/apache-maven/bin
```

```
 source /etc/profile
```

```
$ mvn --version
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
Maven home: /opt/apache-maven
Java version: 1.8.0_262, vendor: Oracle Corporation, runtime: /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.262.b10-0.el7_8.x86_64/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "3.10.0-1062.12.1.el7.x86_64", arch: "amd64", family: "unix"
```

## MySQL for Test

```
sudo yum install -y mysql-server
sudo systemctl start mysqld
```
If you install Cloudera Cluster by the setup script, MySQL/MariaDB is already installed. In this case, use "cloudera" for password.

```
$ mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 1526
Server version: 10.1.46-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> 
```

## Sample Project
```
git clone https://github.com/trK54Ylmz/kafka-spark-streaming-example.git
cd kafka-spark-streaming-example
 mvn clean package -DskipTests
```

### Sample DB
```
CREATE DATABASE IF NOT EXISTS dashboard_test;

USE dashboard_test;

CREATE TABLE IF NOT EXISTS events (
 market VARCHAR(24) NOT NULL DEFAULT '',
 rate FLOAT DEFAULT NULL,
 dt DATETIME NOT NULL,
 PRIMARY KEY (market, dt)
)
ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin;
```

```
show tables
```

### Spark

CDH6.3: Spark 2.4.0

kafka · spark streaming example: Spark 2.0.2

```
Caused by: com.fasterxml.jackson.databind.JsonMappingException: Incompatible Jackson version: 2.10.0
```

https://mvnrepository.com/artifact/org.apache.spark/spark-core_2.12/2.4.0
https://mvnrepository.com/artifact/com.fasterxml.jackson.module/jackson-module-scala_2.13/2.11.2

```
vi common/pom.xml 
```
```
<jackson.version>2.10.0</jackson.version>
```
```
<jackson.version>2.11.2</jackson.version>
```
```
<jackson.version>2.9.9</jackson.version>
```

https://stackoverflow.com/questions/45490778/pass-system-property-to-spark-submit-and-read-file-from-classpath-or-custom-path

```
$ spark-submit --master spark://127.0.0.1:7077 --driver-java-options "-Dconfig=./config/common.conf" streaming/target/spark-streaming-0.1.jar
```

```
spark-submit --master local[*]  --driver-java-options "-Dconfig=./config/common.conf" streaming/target/spark-streaming-0.1.jar
```

Add `--driver-java-options "-Dconfig=./config/common.conf -Dlog4j.configuration=file:log4j.xml"`
```
spark-submit --master local[*]  --driver-java-options "-Dconfig=./config/common.conf -Dlog4j.configuration=file:log4j.xml" streaming/target/spark-streaming-0.1.jar
```
## Reference

Using the CDH 6 Maven Repository

https://docs.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_maven_repo.html

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <repositories>
    <repository>
      <id>cloudera</id>
      <url>https://repository.cloudera.com/artifactory/cloudera-repos/</url>
    </repository>
  </repositories>
</project>
```
