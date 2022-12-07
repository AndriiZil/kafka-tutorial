## Consumer Group Management

1. List Consumer Groups
2. Describe one Consumer Group
3. Delete a Consumer Group

- Producer
```bash
  kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic
```
- Consumer
```bash
  kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic --group my-first-consumer-group
```
- List
```bash
   kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list
```
- Describe
```bash
   kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group my-first-consumer-group
```
- Output
```bash
  GROUP                   TOPIC           PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID     HOST            CLIENT-ID
  my-first-consumer-group first_topic     0          8               8               0               -               -               -
  my-first-consumer-group first_topic     1          14              14              0               -               -               -
  my-first-consumer-group first_topic     2          4               4               0               -               -               -
 
```

### Reset offsets
```bash
  kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group my-first-consumer-group --reset-offsets --to-earliest --execute --all-topics
```
- Output
```bash
   GROUP                          TOPIC                          PARTITION  NEW-OFFSET     
    my-first-consumer-group        first_topic                    0          0              
    my-first-consumer-group        first_topic                    1          0              
    my-first-consumer-group        first_topic                    2          0 
```