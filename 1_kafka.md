# Kafka and Couchbase Kafka Connector

## Setup

### Java Install

```
$ sudo yum install -y java-1.8.0-openjdk-devel
```

### Apache Kafka Download
https://kafka.apache.org/downloads

```
$ curl -OL https://ftp.jaist.ac.jp/pub/apache/kafka/2.6.0/kafka_2.13-2.6.0.tgz
$ tar zxvf kafka_2.13-2.6.0.tgz
```

### Couchbase Kafka Connect Download

```
$ curl -OL https://packages.couchbase.com/clients/kafka/4.0.0/couchbaseinc-kafka-connect-couchbase-4.0.0.zip
$ sudo yum install -y unzip
$ unzip couchbaseinc-kafka-connect-couchbase-4.0.0.zip 
```

### Environment Settings

Set the following environment variables (for example, by adding them to `/etc/pfofile`). The passes would differ depending on where you extracted them.
```
export KAFKA_HOME=/home/ec2-user/kafka/kafka_2.13-2.6.0
export KAFKA_CONNECT_COUCHBASE_HOME=/home/ec2-user/kafka_connector/couchbaseinc-kafka-connect-couchbase-4.0.0
export PATH=$KAFKA_HOME/bin:$PATH
```

## Run the Servers

Run the following scripts in separate windows.
```
$ zookeeper-server-start.sh $KAFKA_HOME/config/zookeeper.properties
```
```
$ kafka-server-start.sh $KAFKA_HOME/config/server.properties
```
Or, you may run them background.
```
nohup zookeeper-server-start.sh $KAFKA_HOME/config/zookeeper.properties > /tmp/kafka-zookeeper.out 2>&1 &
nohup kafka-server-start.sh $KAFKA_HOME/config/server.properties > /tmp/kafka-server.out 2>&1 &
tail -f /tmp/kafka-server.out
```

```
[2020-09-17 00:55:24,631] INFO Kafka version: 2.6.0 (org.apache.kafka.common.utils.AppInfoParser)
[2020-09-17 00:55:24,631] INFO Kafka commitId: 62abe01bee039651 (org.apache.kafka.common.utils.AppInfoParser)
[2020-09-17 00:55:24,631] INFO Kafka startTimeMs: 1600304124627 (org.apache.kafka.common.utils.AppInfoParser)
[2020-09-17 00:55:24,632] INFO [KafkaServer id=0] started (kafka.server.KafkaServer)
```

### Source Configuration

```
$ cd $KAFKA_CONNECT_COUCHBASE_HOME
$ vi etc/quickstart-couchbase-source.properties 
```
```
couchbase.bucket=events
couchbase.username=Administrator
couchbase.password=couchbase
```

### Run the Connector

```
$ cd $KAFKA_CONNECT_COUCHBASE_HOME
$ env CLASSPATH=lib/* \
    connect-standalone.sh $KAFKA_HOME/config/connect-standalone.properties \
                       etc/quickstart-couchbase-source.properties
```

### Check

You can check data flow using Kafka Console Consumer.
```
$ kafka-console-consumer.sh --bootstrap-server localhost:9092 \
                          --property print.key=true \
                          --topic test-default --from-beginning
```

## Reference

https://docs.couchbase.com/kafka-connector/4.0/quickstart.html

https://docs.couchbase.com/kafka-connector/4.0/release-notes.html
