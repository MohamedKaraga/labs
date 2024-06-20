# labs kafka

## Lab 01 : Sep up kafka cluster with Docker  {collapsible="true"}

1. Execute the following command in the terminal to download or copy the contents of the Confluent Platform KRaft all-in-one Docker Compose file:

   ```bash
    wget https://raw.githubusercontent.com/confluentinc/cp-all-in-one/7.6.1-post/cp-all-in-one-kraft/docker-compose.yml
   ```

2. Analyzing the Docker Compose File
3. Start kafka cluster stack with the -d option to run in detached mode:
   ```bash
   docker-compose up -d
   ```
   
4. Show running containers

   ```bash
   docker-compose ps
   ```
   
5. Run inside `broker` container console
   ```bash
   docker exec -it broker /bin/bash
   ```
   
**_run the following command in `broker` container console_**

6. Create the topic `foo`:
   ```bash
   kafka-topics --bootstrap-server kafka:9092 \
   --create \
   --partitions 1 \
   --replication-factor 1 \
   --topic foo
   ```   
   
7. Run `kafka-console-producer` to produce record to topic `foo`:
   ```bash
   kafka-console-producer --bootstrap-server kafka:9092 --topic foo
   ``` 
     
8. Run `kafka-console-consumer` to consume record from topic `foo`:
   ```bash
   kafka-console-consumer \
   --bootstrap-server kafka:9092 \
   --from-beginning \
   --topic foo
   ```
9. Stop kafka cluster stack with -v to remove volumes

   ```bash
   docker-compose down -v
   ```

## Lab 02 : Producer {collapsible="true"}

* Simulate Kafka producer embedded in IoT devices to collect sensor data.
* Configure producers to publish sensor data to Kafka topic representing different sensor.
* Implement mechanisms for data batching, compression to optimize data transmission.

1. Execute the following command in the terminal to clone lab projects :

   ```bash
   git clone https://github.com/MohamedKaraga/labs_kafka.git
   ```

2. go to producer module
   ```bash
   cd labs_kafka/producer
   ```
3. complete code for `ProducerConfig`, `ProducerRecord`, and `KafkaProducer`.
4. Start kafka cluster stack with the -d option to run in detached mode:
   ```bash
   docker-compose up -d
   ```
5. build and run producer
6. Run the producer with different configurations for `linger.ms` and `batch.size` to observe their effects on message batching and sending time.
7. Add compression type to optimize data transmission (`snappy` or `gzip` or `lz4`)
8. Stop kafka cluster stack with -v to remove volumes

   ```bash
   docker-compose down -v
   ```
   
## Lab 03 : Consumer {collapsible="true"}

* Configure Kafka consumer to ingest sensor data from Kafka topic in real-time.
* Process incoming data
* You will learn how to tweak settings such as `fetch.min.bytes`, `fetch.max.wait.ms` to achieve optimal performance.

1. Execute the following command in the terminal to clone lab projects (this step not necessary if you have already done it in the previous lab) :

   ```bash
   git clone https://github.com/MohamedKaraga/labs_kafka.git
   ```

2. go to consumer module
   ```bash
   cd labs_kafka/consumer
   ```
3. complete code for `ConsumerConfig`, `KafkaConsumer`.
4. Start kafka cluster stack with the -d option to run in detached mode:
   ```bash
   docker-compose up -d
   ```
5. build and run consumer

6. Run the consumer with different configurations for `fetch.min.bytes (5000000)`, `fetch.max.wait.ms (5000)` to observe their effects on message consumption and performance.
7. Stop kafka cluster stack with -v to remove volumes

   ```bash
   docker-compose down -v
   ```

## Lab 04 : Basic kafka stream {collapsible="true"}

* You will create a simple Kafka Streams application that reads data from a source topic
* processes it
* and writes the results to a destination topic

1. Execute the following command in the terminal to clone lab projects (this step not necessary if you have already done it in the previous lab) :

   ```bash
   git clone https://github.com/MohamedKaraga/labs_kafka.git
   ```
2. go to kafkastream module
   ```bash
   cd labs_kafka/kafkastream
   ```
3. complete code for `StreamsConfig`, `StreamsBuilder` and `KafkaStreams`.
4. Start kafka cluster stack with the -d option to run in detached mode:
   ```bash
   docker-compose up -d
   ```
5. build and run kafkastream
6. stop kafka cluster stack with -v to remove volumes

```bash
docker-compose down -v
```
