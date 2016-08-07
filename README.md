Kafka in Docker
===

This repository provides everything you need to run Kafka 10 in Docker.


Why?
---
The main hurdle of running Kafka in Docker is that it depends on Zookeeper.
Compared to other Kafka docker images, this one runs both Zookeeper and Kafka
in the same container. This means:

* No dependency on an external Zookeeper host, or linking to another container
* Zookeeper and Kafka are configured to work together out of the box

In the box
---
* **dj/kafka**

  The docker image with both Kafka and Zookeeper. Built from the `kafka`
  directory.


Build from Source
---

    docker build -t dj/kafka kafka/


Run
---
For starting and accessing Kafka from various clients:

```bash
docker run -p 2181:2181 -p 9092:9092 --env \
ADVERTISED_HOST=`docker-machine ip \`docker-machine active\`` \
--env ADVERTISED_PORT=9092 dj/kafka
```

Running the below Producer/Consumer test requires that you start Kafka using (note that this container will self remove after you exit.  If you want it to stick around, remove --rm flag):

```bash
docker run --rm --hostname `docker-machine ip \`docker-machine active\`` \
-p 2181:2181 -p 9092:9092 --env ADVERTISED_HOST=`docker-machine ip \`docker-machine active\`` \
--env ADVERTISED_PORT=9092 dj/kafka
```

The scripts we're executing are embeded in the kafka image so this will ensure that the kafka ports are mapped properly to the docker-machine ip.  

To avoid having to type your container ip over and over again:

```bash
export ZOOKEEPER=`docker-machine ip \`docker-machine active\``:2181
export KAFKA=`docker-machine ip \`docker-machine active\``:9092
```

Create a topic:

```bash
docker run --rm dj/kafka \
kafka-topics.sh --create --zookeeper $ZOOKEEPER --replication-factor 1 --partitions 1 --topic test
```
If you want to confirm creation of topic:

```bash
docker run --rm dj/kafka \
kafka-topics.sh --list --zookeeper $ZOOKEEPER
```

Start a producer:

```bash
docker run --rm --interactive dj/kafka \
kafka-console-producer.sh --topic test --broker-list $KAFKA
```

In a separate window, start a consumer:

```bash
docker run --rm dj/kafka \
kafka-console-consumer.sh --topic test --from-beginning --zookeeper $ZOOKEEPER
```

Type values into producer window and hit enter.  In the consumer window, observe values arriving in order.


