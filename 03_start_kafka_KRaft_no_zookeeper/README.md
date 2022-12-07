## Start Kafka with KRaft (without zookeeper)

- Generate a Cluster UUID
```bash
   KAFKA_CLUSTER_ID="$(bin/kafka-storage.sh random-uuid)"
```
- Format Log Directories
```bash
  bin/kafka-storage.sh format -t $KAFKA_CLUSTER_ID -c config/kraft/server.properties 
```
- Start the Kafka Server
```bash
  bin/kafka-server-start.sh config/kraft/server.properties 
```