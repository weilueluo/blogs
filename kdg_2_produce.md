# Sending Message

> [!NOTE]
>
> not done

Depending on your use case, you might want to configure Kafka differently. Ask yourself, is latency more important than throughput? For example, collection of click information on a web page --- we can tolerate latency, few missing and duplicate of clicks, but we do not want to affect user experience.

### Client

1. A message is created with topic and value, with optionally partition and/or key, this is called a ***Producer Record***.
2. Serializer: the key and message are serialized.
3. Partitioner: a partition is chosen is not present.
4. Adds the message to a batch, grouped by topic and partition.
    1. A batch is sent when it is full, or when some time limit elapsed.
    2. If key is missing, it will be sent to one of the available partitions using round-robin algorithm, if default partitioner is used.
5. A separate thread is responsible for sending those batches to brokers
    1. ***Fire-and-forget*** we do not care if it success.
    2. ***Synchronous send*** we want check the response explicitly
    3. ***Asynchronous send*** we want a function to be invoke when it comes back, asynchronously.

- You can configure the client to be idempotent, so that the same message does not get delivered twice.
- You can configure the number of concurrent messages in flight.
    - Combined with idempotent, you can guarantee exactly once, ordered, delivery that comes with performance penalty.

### Broker

1. When broker receives a message, it returns ***RecordMetadata*** response, with topic, partition and offset of the record in the partition.
2. A error is returned if failed to write message (maybe retried a few times before declaring a failure).

You can also choose how many in-sync replicas replied before sending back a successful response.

### Serialization

For serialization, Apache Avro has the benefit of not needing to change the way you read data if you change your schema, for example, if you have a field called fax number, and you decided to have email for new users, then you can simply add a email field, new users will return non-null for email and null for fax number, old number vice-versa, without you needing to do anything, except to handle the difference when using the fields.