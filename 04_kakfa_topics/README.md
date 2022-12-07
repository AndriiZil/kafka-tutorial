## Topics

1. Create Kafka Topic
2. List Kafka Topics
3. Describe Kafka Topics
4. Increase Partitions in Kafka Topic
5. Delete a Kafka Topic

- Start Server without Zookeeper
```bash
  bin/kafka-server-start.sh config/kraft/server.properties 
```

- List all Topics
```bash
  kafka-topics.sh --bootstrap-server localhost:9092 --list 
```
- Create Topic
```bash
  kafka-topics.sh --bootstrap-server localhost:9092 --create --topic first_topic
  
  kafka-topics.sh --bootstrap-server localhost:9092 --create --topic second_topic --partitions 3
  
  kafka-topics.sh --bootstrap-server localhost:9092 --create --topic third_topic --partitions 3 --replication-factor 1
```
- Describe Topic
```bash
  kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic first_topic
```
- Output
```bash
  Topic: first_topic	TopicId: RgAJna7aRwKOfrnxvzdw6A	PartitionCount: 1	ReplicationFactor: 1	Configs: segment.bytes=1073741824
	        Topic: first_topic	Partition: 0	Leader: 1	Replicas: 1	Isr: 1
```
```bash
  kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic second_topic
```
- Output
```bash
  Topic: second_topic	TopicId: 4qJluMrRRIiE696YiiBw8w	PartitionCount: 3	ReplicationFactor: 1	Configs: segment.bytes=1073741824
          Topic: second_topic	Partition: 0	Leader: 1	Replicas: 1	Isr: 1
          Topic: second_topic	Partition: 1	Leader: 1	Replicas: 1	Isr: 1
          Topic: second_topic	Partition: 2	Leader: 1	Replicas: 1	Isr: 1 
```
```bash
  kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic third_topic
```
- Output
```bash
  Topic: third_topic	TopicId: B_VEjH5DS2OmC9nIpQHksQ	PartitionCount: 3	ReplicationFactor: 1	Configs: segment.bytes=1073741824
          Topic: third_topic	Partition: 0	Leader: 1	Replicas: 1	Isr: 1
          Topic: third_topic	Partition: 1	Leader: 1	Replicas: 1	Isr: 1
          Topic: third_topic	Partition: 2	Leader: 1	Replicas: 1	Isr: 1 
```
- Or describe all topics
```bash
  kafka-topics.sh --bootstrap-server localhost:9092 --describe 
```
- Output
```bash
  Topic: first_topic	TopicId: RgAJna7aRwKOfrnxvzdw6A	PartitionCount: 1	ReplicationFactor: 1	Configs: segment.bytes=1073741824
	        Topic: first_topic	Partition: 0	Leader: 1	Replicas: 1	Isr: 1
  Topic: second_topic	TopicId: 4qJluMrRRIiE696YiiBw8w	PartitionCount: 3	ReplicationFactor: 1	Configs: segment.bytes=1073741824
          Topic: second_topic	Partition: 0	Leader: 1	Replicas: 1	Isr: 1
          Topic: second_topic	Partition: 1	Leader: 1	Replicas: 1	Isr: 1
          Topic: second_topic	Partition: 2	Leader: 1	Replicas: 1	Isr: 1
  Topic: third_topic	TopicId: B_VEjH5DS2OmC9nIpQHksQ	PartitionCount: 3	ReplicationFactor: 1	Configs: segment.bytes=1073741824
          Topic: third_topic	Partition: 0	Leader: 1	Replicas: 1	Isr: 1
          Topic: third_topic	Partition: 1	Leader: 1	Replicas: 1	Isr: 1
          Topic: third_topic	Partition: 2	Leader: 1	Replicas: 1	Isr: 1 
```
- In order to delete Topic type
```bash
  kafka-topics.sh --bootstrap-server localhost:9092 --delete --topic first_topic 
  kafka-topics.sh --bootstrap-server localhost:9092 --delete --topic second_topic 
  kafka-topics.sh --bootstrap-server localhost:9092 --delete --topic third_topic 
```

### Start Producing messages
- Create Topic `first_topic`
```bash
  kafka-topics.sh --bootstrap-server localhost:9092 --create --topic first_topic --partitions 3
```
- Start producing messages
```bash
   kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic
```
- Type messages
```bash
   Hello World
   My name is Andrii Zilnyk
   I Love Kafka
```
- Produce messages with ack
```bash
   kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic --producer-property acks=all
```
- Type messages:
```bash
  some messages that is acked
  just for fun
  we are learning 
```
- Another Example
```bash
   kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic --property parse.key=true --property key.separator=:
```
- Type
```bash
   key:example
   user_id_1234:value
```

### Start Consuming messages
- Only new messages will be appeared
```bash
  kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic
```
- Read messages from the beginning (not by default)
```bash
   kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic --from-beginning
```
- Read messages with params
```bash
  kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic --formatter kafka.tools.DefaultMessageFormatter --property print.timestamp=true --property print.key=true --property print.value=true --from-beginning
```
- Output
```bash
  CreateTime:1670420373602        null    Hello World
  CreateTime:1670420380839        null    My name is Andrii Zilnyk
  CreateTime:1670420385167        null    I love Kafka
  CreateTime:1670420957735        key     example
  CreateTime:1670421172824        null    Its working
  CreateTime:1670421187846        null    test message
  CreateTime:1670421401821        null    hey there
  CreateTime:1670420564279        null    some message that is acked
  CreateTime:1670420569473        null    just for fun
  CreateTime:1670420582649        null    we're learning
  CreateTime:1670420970608        user_id_1234    Andrii
```

### Start Consuming in Group
```bash
  kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic --group my-first-consumer-group
```