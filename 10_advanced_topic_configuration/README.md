# Advanced Topic Configuration

### Start Zookeeper & Kafka Broker
```bash
  bin/zookeeper-server-start.sh config/zookeeper.properties
  
```

### Create new topic
```bash
  ./kafka-topics.sh --create --bootstrap-server localhost:9092 --topic configured-topic --replication-factor 1 --partitions 3
  ./kafka-topics.sh --bootstrap-server localhost:9092 --topic configured-topic --describe
  ./kafka-configs.sh
```

### Configure topic
```bash
  ./kafka-configs.sh --bootstrap-server localhost:9092 --entity-type topics --entity-name configured-topic --alter --add-config min.insync.replicas=2
  
  ./kafka-configs.sh --bootstrap-server localhost:9092 --entity-type topics --entity-name configured-topic --describe
```
- output
```bash
  Dynamic configs for topic configured-topic are:
    min.insync.replicas=2 sensitive=false synonyms={DYNAMIC_TOPIC_CONFIG:min.insync.replicas=2, DEFAULT_CONFIG:min.insync.replicas=1} 
```
- Describe
```bash
  ./kafka-topics.sh --bootstrap-server localhost:9092 --topic configured-topic --describe 
```
- output
```bash
  Topic: configured-topic	TopicId: 6nISEOYpRbiRBl6bEjDPLQ	PartitionCount: 3	ReplicationFactor: 1	Configs: min.insync.replicas=2
	Topic: configured-topic	Partition: 0	Leader: 0	Replicas: 0	Isr: 0
	Topic: configured-topic	Partition: 1	Leader: 0	Replicas: 0	Isr: 0
	Topic: configured-topic	Partition: 2	Leader: 0	Replicas: 0	Isr: 0 
```

### Delete config
```bash
  ./kafka-configs.sh --bootstrap-server localhost:9092 --entity-type topics --entity-name configured-topic --alter --delete-config min.insync.replicas
```
- Topic are made of partitions
- Partitions are made of segments
- `log.segment.bytes` (default 1GB)
- `log.segment.ms` the time Kafka will wait before commit the segment if not full

### Log clean up policies
- `log.cleanup.policy=delete` Delete based on date (1 week by default), Delete based on max size of log (default is -1 == infinite)
- `log.cleanup.policy=compact`
- `log.retantion.hours` - number of hours to keep data is 168h (1 week)
- `log.retantion.bytes` - max size for each partition is (default -1 -infinite)
  - Use cases:
    - `log.retantion.hours=168` and `log.retention.bytes=-1`
    - `log.retenttion.ms=-1` and `log.retention.bytes=524288000`

### Log compaction
- Create topic
```bash
  bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --topic employee-salary --replication-factor 1 --partitions 1 \
  --config cleanup.policy=compact \
  --config min.cleanable.dirty.ratio=0.001 \
  --config segment.ms=5000
```
- success output
```bash
  Created topic employee-salary. 
```
- Describe it
```bash
  bin/kafka-topics.sh --bootstrap-server localhost:9092 --topic employee-salary --describe 
```
```bash
  Topic: employee-salary	TopicId: 1yDteWUNQd2BsvXfmTpthw	PartitionCount: 1	ReplicationFactor: 1	Configs: cleanup.policy=compact,min.cleanable.dirty.ratio=0.001,segment.ms=5000
	Topic: employee-salary	Partition: 0	Leader: 0	Replicas: 0	Isr: 0
```
- Create a consumer
```bash
  bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic employee-salary --from-beginning \
  --property print.key=true \
  --property key.separator=,
```
- Create producer
```bash
   bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic employee-salary \
   --property parse.key=true \
   --property key.separator=,
```
- Type in producer
```bash
  >Patric,salary: 10000
  >Lucy,salary: 20000
  >Bob,salary: 20000
  >Patric,salary: 25000
  >Lucy,salary: 30000
  >Stephane,salary: 0
```
- Create new consumer
```bash
  bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic employee-salary --from-beginning \
  --property print.key=true \
  --property key.separator=,
```
```bash
  Bob,salary: 20000
  Lucy,salary: 30000
  Patric,salary: 30000
  Stephane,salary: 0
```

### Unclean Leader Electrion
- `unclean.leader.election.enable=false` (by default)
- Use cases:
  - Metric collection, log collection, and etc where data loss is somehow acceptable
- If you setup `unclean.leader.election.enable=true` you improove availability, but you will lose data because
- other messages on ISR will be discarded when they come back online and replicate data from the new leader.

### Large messages
- By default in kafka 1MB per message in the topic
- We can modify for it broker, producer nad consumer
- Topic-wise Kafka-side to 10MB
  - on broker side `message.max.bytes`
  - on topic side modify `max.message.bytes`
- Broker wise:
  - `replica.fetch.max.bytes=10485880` (in server.properties)
- Consumer side:
  - `replica.fetch.fetch.bytes=10485880`
- Producer side:
  - `max.request.size=10485880`
