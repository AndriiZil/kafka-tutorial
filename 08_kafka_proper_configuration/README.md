## Kafka Proper Configuration
### Partitions Count & Replication factor

- <6 Brokers 3x #brokers
- >12 Brokers 2x of #brokers

- Should be at least 2, usually 3, max 4
- Never set Replication factor to 1 in prod
- For 3 Replication factor you need to have at least 3 brokers

- Total number of partitions in cluster:
    - Kafka with Zookeeper max 200.000 partitions (Zookeeper scaling limit)
    - Still recommended a maximum 4.000 partitions per broker (soft limit)
    - Kafka with Kraft potentially millions of partitions
    - If you need more partitions in your cluster, add brokers instead
    - If you need more than 200.000 partitions in your cluster create more Kafka clusters
    - You don't need a topic with 1000 partitions to achieve high throughput

### Topic naming convention

<message type>.<dataset name>.<data name>.<data format>
 - message type: loggong, queuing, push, user, streaming
 - dataset name is analous to a database name in traditional RDBMS systems
 - data format for example .avro, .json, .text, .protobuf, .csv, .log
