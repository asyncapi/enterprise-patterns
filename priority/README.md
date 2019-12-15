# Message priority

The following is an example of how we can define message priority using the AMQP 1.0 binding.

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
