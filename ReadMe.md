# Kafka on Docker (KRaft) — Minimal Producer/Consumer Big Data Demo

## Project description
This project installs and runs **Apache Kafka** locally using **Docker Compose** (KRaft mode, no ZooKeeper) and demonstrates a minimal working example with a **console producer/consumer**. The repository includes logs and screenshots proving execution.

## Chosen tool
- **Apache Kafka** (event streaming platform) running in a Docker container.

## Why I selected this tool
Kafka is a foundational Big Data component used to ingest and distribute streams of events (logs, clicks, IoT signals, transactions). It is widely used as the “central bus” between producers (apps/services) and downstream systems (stream processing, storage, analytics).

## Setup and installation (commands I ran)

### Prerequisites
- Docker + Docker Compose
- Git

### Folder structure

kafka-bigdata-tool/
├─ docker-compose.yml
├─ logs/
├─ screenshots/
├─ scripts/
├─ sample-data/
└─ src/

### Start Kafka with Docker Compose
```bash
docker compose up -d
docker ps
```
Capture broker logs (proof)
```bash
docker logs broker --tail 50
docker logs broker > logs/broker_full.log
docker logs broker --tail 80 > logs/broker_tail.log
```
Minimal working example (topic + producer + consumer)

1) Open a shell in the Kafka container
```bash
docker exec -it -w /opt/kafka/bin broker sh
```
2) Create a topic
```bash

./kafka-topics.sh --create --topic my-topic --bootstrap-server broker:29092
```
3) Producer (send 2 messages, then CTRL+C)
```bash

./kafka-console-producer.sh --topic my-topic --bootstrap-server broker:29092
```
Type:

All streams
lead to Kafka

Then press CTRL + C.

4) Consumer (read messages, then CTRL+C)
```bash

./kafka-console-consumer.sh --topic my-topic --from-beginning --bootstrap-server broker:29092
```
Expected output:

All streams
lead to Kafka

Then press CTRL + C and exit:

exit

5) Save execution proofs (topics + consumed messages)
```bash

docker exec -w /opt/kafka/bin broker sh -lc './kafka-topics.sh --list --bootstrap-server broker:29092' | tee logs/topics_list.txt
docker exec -w /opt/kafka/bin broker sh -lc './kafka-console-consumer.sh --topic my-topic --from-beginning --max-messages 2 --bootstrap-server broker:29092' | tee logs/consume_proof_clean.txt
```
Proof of execution (screenshots & logs)
	•	Screenshots:
	•	screenshots/01_docker_ps.png (container running)
	•	screenshots/02_consumer_output.png (messages consumed)
	•	screenshots/03_broker_logs.png (broker started)
	•	Logs:
	•	logs/broker_tail.log / logs/broker_full.log
	•	logs/topics_list.txt
	•	logs/consume_proof_clean.txt

How Kafka fits in a Big Data ecosystem

Kafka is commonly used as the ingestion and event backbone:
	•	Producers: web apps, backend services, IoT devices, CDC connectors
	•	Kafka topics: durable event streams
	•	Consumers:
	•	Stream processing (Spark Structured Streaming, Flink, Kafka Streams)
	•	ETL / routing (NiFi, Kafka Connect)
	•	Storage/analytics (HDFS/S3, data lakehouse, Elasticsearch, OLAP)

This enables decoupled, scalable pipelines (multiple consumers can independently read the same stream).

Challenges encountered + how I solved them
	1.	Docker daemon not running

	•	Error: Cannot connect to the Docker daemon ... docker.sock
	•	Fix: start Docker Desktop, then re-run:

docker compose up -d

	2.	Consumer timeout in proof logs (optional)

	•	When using --timeout-ms, the console consumer may end with a TimeoutException once no new messages arrive.
	•	Fix: use --max-messages 2 to stop cleanly after reading exactly 2 messages:

./kafka-console-consumer.sh --topic my-topic --from-beginning --max-messages 2 --bootstrap-server broker:29092

My Setup Notes (what I learned)
	•	Kafka can run in KRaft mode (no ZooKeeper), which simplifies local setups.
	•	The key point to make CLI tools work in Docker is using the correct bootstrap address:
	•	inside the container/network: broker:29092
	•	from the host: localhost:9092

Stop / cleanup

docker compose down -v

