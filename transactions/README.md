# Transactions

Some brokers allow us to send messages as part of a transaction. For this to work, we need to specify a "transaction id" as part of our message metadata. The following is an example of how to specify the transaction id when sending a message to Kafka.

## Kafka's "transactional id"

In the case of Kafka, the transaction id is part of the protocol and can be set on producers as `transactional.id`. We can define it with AsyncAPI as follows:

```yaml
...
channels:
  myTopic:
    publish:
      bindings:
        kafka:
          transactionalId: my-transactional-id
      message:
        ...
```

As shown above, `transactionalId` is a property of the `kafka` operation binding.