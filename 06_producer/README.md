## Producer

- `min.insync.replicas=1` = only the broker leader needs to be successfully ask
- `min.insync.replicas=2` = at least one broker leader and one replica need to ask
- `asks=0&asks=1` if one partition is up and considered as ISR, the topic will be available for writes
- `asks=all`:
    - `min.sync.replicas=1` (default) the broker must have at least 1 partition up as an ISR (that includes the leader)
    - and so wwe can tolerate two brokers being down
    - `min.insync.replicas=2` the topic must have at least 2 ISR up, therefore we can tolerate at most
    - one broker being down (in case replication factor of 3), and we have a guarantee that for every write,
    - the data will be at least written twice
    - `min.insync.replicas=3` this wouldn't make sense for corresponding replication factor of 3 and we
    - couldn't tolerate any broker going down
    - in summary, when `acks=all` with `replication.factor=N` and `min.insync.replicas=M` we can tolerate N-M brokers 
    - going down for topic availability purposes
    - `asks=all` and `min.insync.replicas=2` is the most popular option for data durability and 
    - availability and allows you to withstand at most the loss one Kafka broker

### Retries
- `retries` settings
- defaults to 0 for Kafka <=2.0
- defaults to 2147483647 for Kafka >= 2.1
- The `retry.backoff.ms` setting is by default 100ms
- Since Kafka 2.1 you can set `delivery.timeout.ms=120000` = min
- Records will be failed if they can't be acknowledged within `delivery.timeout.ms`
- If you rely on key-based ordering, that can be an issue:
    - For this you can set setting while controls how many produce requests can be made in parallel `max.in.flight.requests.per.connection`
    - Default: 5
    - Set it to 1 if you need to ensure ordering (may impact throughput)

### Idempotent Producer
- In Kafka >= 0.11 you can define a `idempotent producer` which won't introduce
- duplicates on network error, don't commit message twice
- In Kafka since Kafka 3.0 is default

### Defaults
- Since Kafka 3.0 the producer is safe by default
- `acks=all` (-1)
- `enable.idempotence=true`
- With Kafka 2.8 and lower the producer by default comes with:
- `acks=1`
- `enable.idempotence=false`

### Recommended settings
- `acks=all` Ensure data is properly replicated before an ack is received
- `min.insync.replicas=2` (broker/topic level) Ensure two brokers in ISR at least have the data after an ack
- `enable.idenpotence=true` Duplicates are not included due to the network retries
- `reties=MAX_INT` (producer level) Retry until delivery.timeout.ms is reached
- `delivery.timeout.ms=12000` Fail after retrying for 2 min
- `max.in.flight.requests.per.connection=5` Ensure maximum performance while keeping message ordering

### Message Compression at the Producer level
- Producer usually send data that is text-based for example JSON data
- In this case is important to apply compression to the producer
- Compression can be enabled at the Producer level and doesn't require any configuration change in the Brokers or Consumers
- `compression.type` can be `none` (default), `gzip`, `lz4`, `snappy`, `zstd` (Kafka 2.1)
1. Much smaller producer requests size (compression ratio up to 4x)
2. Faster to transfer data over the network
3. Better throughput
4. Better disk utilization in Kafka (stored messages are smaller)
5. Consider testing `snappy` or `lz4`
- `compression.type=producer` (default)
- `compression.type=none` all buthches are decompressed by the broker
- `compression.type=lz4`
- If you enable broker-side compression, it will consume extra CPU cycles

### Batch mechanism on Producer
- `linger.ms` (default 0) how to long to wait untill we send a batch. Adding a small
- number for example 5ms helps add more messages in the batch at the expense of latency
- `batch.size` (default 16Kb 16*1024=16384) if a batch is filled before `linger.ms` increase the batch size
- Increasing the batch size to 32Kb or 64Kb (32*1024 or 64*1024) can help increasing the compression, throughput, and efficiency of requests
- Any message that is bigger than the batch size will not be batched
- A batch is allocated per partition, don't set it to a number that's too high
- Also introduce some producer-level compression for more efficiency in sends
- A good example is:
  - Message compression `snappy` when using text based or JSON documents
  - `snappy` has a goof balance of CPU / compression ratio
  - Increase `batch.size` to 32Kb
  - Small delay `linger.ms` (20ms)
