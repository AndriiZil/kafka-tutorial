## Kafka Installation (Ubuntu 22.04)

### Check Java version
```bash
    java -version
```
### Install if version less than 11
```bash
    sudo apt install default-jre
```

## Kafka Start Server

### Start Zookeeper (from Kafka directory)
```bash
    bin/zookeeper-server-start.sh config/zookeeper.properties
```

### Start Kafka Broker
```bash
    bin/kafka-server-start.sh config/server.properties
```

### Terminal setup (Linux)
- Find file `.bashrc`
```bash
  sudo nano ~/.bashrc 
```
- Add path to Kafka like that (example) to the end
```bash
   PATH="$PATH:/home/andrii/kafka_2.13-3.3.1/bin"
```
- In this case start Zookeeper with command
```bash
  zookeeper-server-start.sh ~/kafka_2.13-3.3.1/config/zookeeper.properties 
```
- Start Kafka
```bash
   kafka-server-start.sh ~/kafka_2.13-3.3.1/config/server.properties
```

### Log dirs
- Zookeeper `/home/andrii/kafka_2.13-3.3.1/config/zookeeper.properties`
```bash
  dataDir=/tmp/zookeeper 
```
- Kafka `/home/andrii/kafka_2.13-3.3.1/config/server.properties`
```bash
  log.dirs=/tmp/kafka-logs 
```