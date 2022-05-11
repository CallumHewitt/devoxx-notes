# Developing a stream processing application with Apache Kafka and Quarkus

2022-05-11 17:30
Ozan Gunalp
Principal Software Engineer @ Red Hat
@ozangunalp

## Apache Kafka

A distributed event processing platform. Conceptually it's a log file, producers append, consumers pick an index and start reading through it.

It's distributed, wanting to be scalable. Each file is divisioned into topic partitions and replicated around the Kafka nodes. The consumers read the topic partitions from any given offset. The topic partition is totally ordered but not the topic itself.

Consumers are assigned topic partitions by the servers and are given offsets etc. to read from.

## Kafka Consumer Loop

Typical Kafka consumer loop.

Create a consumer.
Subscribe the consumer to a topic.
while(true) -> poll the consumer and read the list of records, process those records.

There are a lot of problems here

- The consumer continues to poll Kafka with the poll method to make the server know that it's alive. If it blocks while processing then you're going to have issues. You need to keep the consumer heartbeat alive.
- You need to commit the offsets of the records that you've processed.

## Quarkus

Quarkus is a kubernetes-native Java Stack optimised for container workloads.

- Pre-built integrations, small container size, increased deployment density.
- Dev Mode with live reload, Dev services, continuous testing
- Quarkus extensions, low memory overhead + fast startup time
- Reactive + Imperative - High concurrency + low thread usage

You're not obliged to use reactive programming. You can use both.

## SmallRye Reactive Messaging

Framework for building event-driven applications. Supports various techs like Kafka, AMQP, MQTT, Camel. Declarative development model using CDI.

```java
@Incoming("incoming-topic")
@Outgoing("outgoing-topic")
public String doTheThing(String in) {}
```

## Demo

Site -> Write --> [click topic] --> Persist to MongoDB --> [clicks-persisted topic] --> Aggregate Kafka Streams --> [clicks-per-element topic] --> Visualize --> Metrics Dashboard

## The Clicks Resource

```java
@Channel("clicks-out")
MutinyEmitter<PointerEvent> emitter

public Uni<Void> postClick(PointerEvent click) {
    Uni<Void> uni = emitter.send(click);
    return uni;
}
```

A uni is the call back for the reactive programming model.

## Clicks Persistence

```java
@Incoming("clicks")
@Outgoing("clicks-persisted")
public Message<PointerEventDTO> clicks(Message<PointerEvent> message) {
    PointerEventDTO dto = persist(message)
    return message.withPayload(dto)
}
```

## The Kafka Streams Aggregation

```java
@Produces
public Topology topology() {
    StreamsBuilder builder = new StreamsBuilder();
    Serde<PointerEventDTO> pointerEventSerde = ...
    builder.stream(CLICKS_TOPIC, Consumed.with(Serdes.String(), pointerEventSerde))
        .groupBy()
        ...
        ...
        .toStream() // KStream<String, Long>
        .to(CLICKS_PER_ELEMENT_TOPIC, Product.with(Serdes.String(), Serdes.Long()s))
    return builder.build()
}

@Channel(CLICKS_PER_ELEMENT_TOPIC)
Publisher<KafkaRecord<String, Long>> clickCount;
```

The output of this is then available to read from the topic in the same service by doing `clickCount.toItem()`.