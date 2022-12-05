## Kafka Topics

* Like a table in a database
* You can have as many topics as you want
* Identified by its name
* Any kind of message format
* The sequence of messages is called a data stream
* You cannot query topics, instead use Kafka Producers to send data and Kafka Consumers to read the data

## Topics are split in partitions (example 100 partitions)

* Message within each partition are ordered
* Each message within a partition gets an incremental ID, called offset
* Topics area immutable: once data is written to a partitions, it cannot be changed
* Data is kept only for a limited time (default is one week - configurable 168h)
* Offset only have a meaning for a specific partition
* Order is guaranteed only within a partition (not across partitions)
* Data is assigned randomly to a partition unless a key is provided
* You can have as many partitions per topic as you want

## Producers

* Producers write data to topics (which are made of partitions)
* Producers know to which partitions to write to (and which kafka broker has it)
* In case of Kafka broker failures, Producers will automatically recover
* Producers ca choose to send key with the message (string, number, binary, etc..)
* If `key=null` data is send Round Robin
* If `key!=null` all the messages for that key will always go to the same partition
* A "key" are typically sent if you need message ordering for a specific field

## Message

* Key - binary (can be null)
* Value - binary (can be null)
* Compression Type (none, gzip, snappy, lz4, zstd)
* Headers (optional) - Key, Value
* Partitions + Offset
* Timestamp (system or user set)

## Kafka Message Serializer

* Kafka only accepts bytes as an input from producers and sends bytes out as an output to conumers
* Message Serialization means transforming objects / data into bytes
* There are used on the value and they
* Common Serializers (String, Int, Float, Avro, Protobuf)

## Consumers

* Consumers read data from a topic (identified by name) - pull model
* Consumers automatically know which broker to read
* In case of broker failures, consumers know how to recover
* Data is read in order from low to high offset within each partitions

## Consumer Deserializer

* Deserialize indicates how to transform bytes into objects / data
* They are used on the value and the key of the message
* The serialization / deserialization type must not change during a topic lificycle (create a new topic instead)

## Consumer Groups

* Each consumer within a group reads from exclusive partitions
* If you have more consumers than partitions, some consumers will be inactive
* In Apache Kafka it is acceptable to have multiple consumer groups on the same topic
* To create distinct consumer groups. use the consumer property `group.id`

## Consumer Offsets

* Kafka stores the offsets at which a consumer group has been reading
* The offsets commited are in Kafka topic named __consumer_offsets
* When a consumer in a group has processed data received from Kafka, it should be periodically committing the offsets
  (the Kafka broker will write to __consumer_offsets, not the group itself)
* If a consumer dies, it will be able to read back from where it left off thanks to the committed consumer offsets!

## Kafka Brokers

* A Kafka Cluster is composed of multiple brokers (servers)
* Each broker is identified with ID (integer)
* Each broker contains topic partitions
* After connecting to any broker (called a bootstrap broker), you will be connected to the entire cluster (Kafka clients have
smart mechanics for that)
* A good number to get started is 3 brokers, but some big clusters have over 100 brokers

## Topic replication factor

* Topic should have a replication factor > 1
* This way if broker is down, another broker can serve data
* At any time only ONE broker can be a leader for a given partition
* Producers can only send data to the broker that is leader of a partition
* Kafka Producers can only write to the leader broker for a partition
* Kafka Consumers by default will read from the leader broker for a partition

## Producer Acknowledgements

* Producers can choose to receive acknowledgment of data writes:
* acks=0 Producer won't wait for acknowledgment (possible data loss)
* acks=1 Producer will wait for leader acknowledgment (limited data loss)
* acks=all Leader + replicas acknowledgment (no data loss)

## Zookeeper

* Zookeeper manages brokers (keeps a list of them)
* Zookeeper helps in performing leader election for partitions
* Zookeeper sends notifications to Kafka in case of changes (e.g. new topic, broker dies, broker comes up, delete topics, etc...)
* Kafka 2.x can't work without Zookeeper
* Kafka 3.x can work without Zookeeper (KIP-500) - using Kafka Raft instead
* Kafka 4.x will not have Zookeeper
* Zookeeper by design operates with odd number of servers (1, 3, 5, 7)
* Zookeeper has a leader (writes) the rest of the servers are followers (reads)

## About Kafka KRaft

* In 2020 the Apache Kafka project started to work to remove the Zookeeper dependency from it (KIP-500)
* Zookeeper shows scaling issues when Kafka clusters have > 100.000 partitions
* By removing Zookeeper Apache Kafka can:
- Scale to millions of partitions and becomes easier to maintain and set up
- Improve scalability, makes it easier to monitor, support and administer
- Single security model for the whole system
- Single process to start wit Kafka
- Faster controller shutdown and recovery time