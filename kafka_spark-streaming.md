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

## Sample Project
```
git clone https://github.com/trK54Ylmz/kafka-spark-streaming-example.git
cd kafka-spark-streaming-example
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
