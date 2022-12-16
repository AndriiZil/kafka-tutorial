# Consumer Offsets

- `auto.offset.reset=latest` will read from the end of the log
- `auto.offset.reset=earliest` will read from the start of the log
- `auto.offset.reset=none` will throw exception if no  offsets is found

- Additionally, consumer data will be lost if:
  - If a consumer has not read new data in 7 days (Kafka > 2.0v)
  - This can be controlled by `offset.retention.minutes` parameter

### Consumer Poll Behavior
- `max.partition.fetch.bytes` default is 1MB, The maximum amount of data per partition the server will return
- `fetch.max.bytes` (default 55MB), Maximum data returned for each fetch request
- If you have available memory try to increasing `fetch.max.bytes` to allow the consumer to read  more data in each request
