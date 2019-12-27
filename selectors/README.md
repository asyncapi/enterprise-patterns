# Selectors (aka filters)

Selectors are also known as filters in AMQP 1.0. This document goes through all the filters AMQP 1.0 broker implementations offer, and explains how to document them with AsyncAPI.

## Introduction

All information related to filters, should be placed in the `amqp1` operation binding, i.e:

```yaml
...
channels:
  mychannel:
    subscribe:
      bindings:
        amqp1:
          filters:
            ...
```

## Legacy AMQP exchange binding support

The following filters are meant to mimic the binding types of AMQP 0-9-1.

These filters are mutually exclusive, i.e., they can't be used together but can be combined with filters from the other categories.

### Legacy AMQP direct binding

This filter matches the behavior of a [direct exchange](https://www.rabbitmq.com/tutorials/amqp-concepts.html#exchange-direct) in AMQP 0-9-1.

See its [definition](https://svn.apache.org/repos/asf/qpid/trunk/qpid/specs/apache-filters.xml#type-legacy-amqp-direct-binding) for more information.

```yaml
...
channels:
  my-routing-key:
    subscribe:
      bindings:
        amqp1:
          filters:
            - type: 'apache.org:legacy-amqp-direct-binding'
```

### Legacy AMQP topic binding

This filter matches the behavior of a [topic exchange](https://www.rabbitmq.com/tutorials/amqp-concepts.html#exchange-topic) in AMQP 0-9-1.

See its [definition](https://svn.apache.org/repos/asf/qpid/trunk/qpid/specs/apache-filters.xml#type-legacy-amqp-topic-binding) for more information.

```yaml
...
channels:
  /{airlineCode}/{flightCode}/updates:
    subscribe:
      bindings:
        amqp1:
          filters:
            - type: 'apache.org:legacy-amqp-topic-binding'
              value: '*.*.updates' # Other examples: IBE.*.updates, IBE.#, IBE.IB8313.updates, etc.
```

In this case, `value` is optional and defaults to the AMQP topic representation of the channel name. It's possible to change the value to something completely different but you should consider if that makes sense at all, since that would probably mean you want to change the channel name instead.

### Legacy AMQP headers binding

This filter matches the behavior of a [headers exchange](https://www.rabbitmq.com/tutorials/amqp-concepts.html#exchange-headers) in AMQP 0-9-1.

See its [definition](https://svn.apache.org/repos/asf/qpid/trunk/qpid/specs/apache-filters.xml#type-legacy-amqp-headers-binding) for more information.

```yaml
...
channels:
  flight-updates:
    subscribe:
      message:
        headers:
          type: object
          properties:
            airlineCode:
              type: string
            flightCode:
              type: string
          required:
            - airlineCode
        payload:
          ...
      bindings:
        amqp1:
          filters:
            - type: 'apache.org:legacy-amqp-headers-binding'
              value:
                airlineCode: IBE
```

In this case, `value` is required and MUST be a map of key/value pairs. The filter MUST pass when and only when **all** the headers match the given value.

## JMS

### No local filter

This filter is meant for telling the broker that a message must be accepted if and only if the message was originally sent to the container of the source on a separate connection from that which is currently receiving from the source. In other words, this filter is for not receving your own messages in scenarios where you're publishing and subscribing to the same channel.

See its [definition](https://svn.apache.org/repos/asf/qpid/trunk/qpid/specs/apache-filters.xml#type-no-local-filter) for more information.

```yaml
...
channels:
  /{airlineCode}/{flightCode}/updates:
    subscribe:
      bindings:
        amqp1:
          filters:
            - type: 'apache.org:no-local-filter'
```

This filter can be combined with other filters, e.g:

```yaml
...
channels:
  /{airlineCode}/{flightCode}/updates:
    subscribe:
      bindings:
        amqp1:
          filters:
            - type: 'apache.org:no-local-filter'
            - type: 'apache.org:legacy-amqp-topic-binding'
              value: '*.*.updates'
```

### Selector filter

This filter allows you to select what information you want to get from the channel. It uses a SQL-like syntax.

See its [definition](https://svn.apache.org/repos/asf/qpid/trunk/qpid/specs/apache-filters.xml) for more information.

```yaml
...
channels:
  /{airlineCode}/{flightCode}/updates:
    subscribe:
      message:
        headers:
          type: object
          required:
            - departureCountry
          properties:
            departureCountry:
              type: string
        payload:
          ...
      bindings:
        amqp1:
          filters:
            - type: 'apache.org:selector-filter'
              value: departureCountry = 'Spain' OR departureCountry = 'USA'
```

## XQuery

This filter allows you to select what information you want to get from the channel. It uses the [XQuery](https://www.w3.org/XML/Query/) syntax.

See its [definition](https://svn.apache.org/repos/asf/qpid/trunk/qpid/specs/apache-filters.xml) for more information.

```yaml
...
channels:
  /{airlineCode}/{flightCode}/updates:
    subscribe:
      message:
        payload:
          type: object
          properties:
            flight:
              type: object
              properties:
                code:
                  type: string
                ...
          ...
      bindings:
        amqp1:
          filters:
            - type: 'apache.org:xquery-filter'
              value: /flight/code[starts-with('IB')]
```

