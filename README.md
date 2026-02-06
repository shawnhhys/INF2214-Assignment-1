# INF2214 Assignment 1 – Campus Cafe Event Pipeline

## Overview

This project implements a **Kafka-based streaming pipeline** for a campus cafe. Each customer interaction (order, loyalty card scan, etc.) is emitted as a JSON event. The pipeline consists of a **producer** that sends events to a Kafka topic and a **consumer** that reads and prints them.

---
## 1. Docker Setup

1. Start Docker containers for Kafka broker and Schema Registry:
docker-compose up -d

2. Verify the containers are running:
docker ps

3. Create the Kafka topic demo with 3 partitions and replication factor 1:
docker exec -it broker kafka-topics --create --topic demo --bootstrap-server localhost:9092 --partitions 3 --replication-factor 1
Deleting and recreating the topic is could make sure each time the terminal will output 5 results

## 2.  JSON Schema
| Field           | Type   | Description                              |
| --------------- | ------ | ---------------------------------------- |
| `event_id`      | string | Unique identifier for each event         |
| `customer_name` | string | Name of the customer                     |
| `drink_name`    | string | Name of the drink ordered                |
| `quantity`      | int    | Number of items                          |
| `amount`        | float  | Total cost of the order                  |
| `is_member`     | bool   | Whether the customer is a loyalty member |
| `ts_utc`        | string | UTC timestamp of the event               |

## 3. Producer Configuration (producer.py)

Sends 5 random events per run.
Kafka configuration:

`acks`: 1                 # Waits for leader confirmation
`client.id`: <hostname>   # Identifies the producer client
`key.serializer`: StringSerializer("utf_8")
`value.serializer`: JSONSerializer(...)


Each event is delivered to a partition in the demo topic.
Delivery confirmation is printed using a callback function.

## 4. Consumer Configuration (consumer.py)

Kafka consumer is configured as follows:

`group.id': 'cafe-group-clean`       # Unique identifier for this consumer group
`auto.offset.reset': 'earliest`      # Start reading from the earliest offset if group is new
`enable.auto.commit`: True           # Automatically commit offsets after messages are read

group.id ensures independent tracking of consumed messages.
auto.offset.reset allows a fresh consumer to read all messages.
enable.auto.commit simplifies offset management for this demo.

## 5. Running the Pipeline
Start the consumer (optional, can run before or after producer):
'py consumer.py'

Run the producer:
'py producer.py'

Verify in the consumer terminal that exactly 5 messages are received.
