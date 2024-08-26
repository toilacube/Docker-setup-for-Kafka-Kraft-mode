# Setup and use Docker for Apache Kafka (Kraft mode, no zookeeper)

OS: Ubuntu 22.04

- [Docker compose file](#docker-compose-file)
- [Basic commands](basic-commands)
- [Log files](log-files)

## Docker compose file <a name="docker-compose-file"></a>

Use `docker-compose.yml` file to start the Kafka server:

```
services:
  kafka:
    image: apache/kafka:latest
    container_name: kafka
    ports:
      - "9092:9092"
    environment:
      KAFKA_NODE_ID: 1
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      KAFKA_LISTENERS: PLAINTEXT://:9092,CONTROLLER://:9093
      KAFKA_CONTROLLER_QUORUM_VOTERS: 1@kafka:9093
      KAFKA_PROCESS_ROLES: broker,controller
      KAFKA_CONTROLLER_LISTENER_NAMES: CONTROLLER
    volumes:
      - kafka_data:/var/lib/kafka/data
volumes:
  kafka_data:

```

And then:

```
docker compose up
```

**Note:** Docker will automatically add container name as a prefix before the volume name, so the `/var/lib/kafka/data` of the container will be mouted from docker volume named `kafka_kafka_data`

## Step to use basic commands in Kafka with docker <a name="basic-commands"></a>

**Enter the shell of Kafka container:**

This part may differ on other OS.

To enter the shell of running Kafka container:

```
docker exec -it kafka /bin/sh
```

Move to `/opt/kafka`:

```
cd /opt/kafka
```

After that you can start using basic commands like from the offical tutorial from [Apache](https://kafka.apache.org/quickstart):

- Create a topic

```
bin/kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092
```

- Describe a topic

```
 bin/kafka-topics.sh --describe --topic quickstart-events --bootstrap-server localhost:9092
```

## Log files <a name="log-files"></a>

To check where the log files are located:

```
cd /opt/kafka/config

cat server.properties
```

The `log.dirs` will show where the files are stored. If it doesn't exist then it will be stored in default localtion `/tmp/kafka-logs`
