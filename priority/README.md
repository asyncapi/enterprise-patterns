# Priority queues

AMQP 1.0 removes the semantics of queues and exchanges that were present in AMQP 0-9-1. Therefore, this interpretation is left to broker implementations.
The following is an example of how we can define priority queues and message priority using the AMQP 1.0 binding.

## Defining the priority for a queue

At the channel `amqp1` binding level, we have a `queue` field containing two properties that we can set up:

1. The `priority` property: it's a boolean and defaults to `false`. When `true`, it means the queue implements a priority policy.
1. The `maxPriority` property: it's an unsigned integer. When set, it defines the maximum priority this queue handles.

###### Example

```yaml
channels:
  /{airlineCode}/{flightCode}/updates:
    bindings:
      amqp1:
        queue:
          name: prioritizedQueue1
          priority: true
          maxPriority: 10
```

> **Note:** Even though the AMQP 1.0 protocol doesn't have the concept of "queues", most of the implementations (if not all) offer ways to bind queues to addresses. Therefore, the queue information defined here is actually meant for implementation purposes, and should not be seen as a protocol mapping.

## Defining the priority of a message

At the message `amqp1` binding level, we have the `priority` field. It maps directly to the message `priority` field on AMQP 1.0. Its value MUST be a positive integer defining the priority of the message.

###### Example

```yaml
components:
  messages:
    myMessage:
      ...
      bindings:
        amqp1:
          priority: 4
```

> **Note:** It's up to the implementation to decide what happens when `priority` is higher than `maxPriority`.