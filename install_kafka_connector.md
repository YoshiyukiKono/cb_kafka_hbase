https://docs.couchbase.com/kafka-connector/4.0/quickstart.html

https://docs.couchbase.com/kafka-connector/4.0/release-notes.html

## Couchbase Kafka Connect

```
curl -OL https://packages.couchbase.com/clients/kafka/4.0.0/couchbaseinc-kafka-connect-couchbase-4.0.0.zip

sudo yum install -y unzip

unzip couchbaseinc-kafka-connect-couchbase-4.0.0.zip 
```

## Java

```
sudo yum install -y java-1.8.0-openjdk-devel
```

```
yum list available \*openjdk\*
```
## Kafka
https://kafka.apache.org/downloads

```
curl -OL https://ftp.jaist.ac.jp/pub/apache/kafka/2.6.0/kafka_2.13-2.6.0.tgz

tar zxvf kafka_2.13-2.6.0.tgz
```

```
$ cd kafka_2.13-2.6.0
$ export KAFKA_HOME=`pwd`
$ echo $KAFKA_HOME
/home/ec2-user/kafka/kafka_2.13-2.6.0
$ export PATH=$KAFKA_HOME/bin:$PATH
```
```
zookeeper-server-start.sh $KAFKA_HOME/config/zookeeper.properties
kafka-server-start.sh $KAFKA_HOME/config/server.properties
```

### Configure the Source Connector

```
$ vi etc/quickstart-couchbase-source.properties 
```
```
couchbase.bucket=travel-sample
couchbase.username=Administrator
couchbase.password=couchbase
```

### Run the Source Connector

```
cd $KAFKA_CONNECT_COUCHBASE_HOME
env CLASSPATH=lib/* \
    connect-standalone.sh $KAFKA_HOME/config/connect-standalone.properties \
                       etc/quickstart-couchbase-source.properties
```

### Run the Consumer

```
kafka-console-consumer.sh --bootstrap-server localhost:9092 \
                          --property print.key=true \
                          --topic test-default --from-beginning
```
