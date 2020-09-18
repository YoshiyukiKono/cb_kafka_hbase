# Data Integration

## CB
```
cbimport json -c couchbase://127.0.0.1 -u Administrator -p couchbase \
 -b events -d file:///sample.txt -f lines -g '#UUID#'
```

```
{"type":"europe","value":439.82892}
{"type":"europe","value":158.33551}
```

## HBase

```
hbase shell
```

```
create 'events', 'cf1'
```

## MySQL

```
CREATE TABLE IF NOT EXISTS events (
 market VARCHAR(24) NOT NULL DEFAULT '',
 rate FLOAT DEFAULT NULL,
 dt DATETIME NOT NULL,
 PRIMARY KEY (market, dt)
)
ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin;
```

## Kafka Connector


```
vi $KAFKA_CONNECT_COUCHBASE_HOME/etc/quickstart-couchbase-source.propertie
```
```
couchbase.bucket=events
couchbase.username=Administrator
couchbase.password=couchbase
```
