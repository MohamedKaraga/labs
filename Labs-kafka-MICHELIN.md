# labs kafka

## Lab 01 : Without Docker  {collapsible="true"}

Setting up a Kafka cluster with KRaft (Kafka Raft) mode involves configuring broker to operate without relying on Zookeeper. 
KRaft mode simplifies Kafka's architecture by managing metadata through an internal consensus protocol.
Here's a step-by-step guide to set up a Kafka cluster in KRaft mode.

**_Prerequisite_** : Ensure **JDK 8 or higher** is installed and configured on your system.

1. Download Kafka

   ```bash
   wget https://downloads.apache.org/kafka/3.7.0/kafka_2.13-3.7.0.tgz
   ```

2. Extract the downloaded file

   ```bash
   tar -xzf kafka_2.13-3.7.0.tgz && cd kafka_2.13-3.7.0
   ```

3. Configure Kafka Broker

Create a configuration file `server.properties` and configure the broker.

   ```bash
   # KRaft-specific configurations
   process.roles=broker,controller
   node.id=1
   controller.quorum.voters=1@localhost:9093
   
   # Listeners
   listeners=PLAINTEXT://localhost:9092,CONTROLLER://localhost:9093
   controller.listener.names=CONTROLLER
   listener.security.protocol.map=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
   
   # Log directories
   log.dirs=/var/lib/kafka/data
   metadata.log.dir=/var/lib/kafka/meta
   
   # Enable topic auto-creation
   auto.create.topics.enable=true
   ```

4. Create Required Directories

   ```bash
   mkdir -p /var/lib/kafka/data
   mkdir -p /var/lib/kafka/meta   
   ```

5. Generate a new ID for your cluster and Format the Metadata Directory

   ```bash
   bin/kafka-storage.sh format -t $(bin/kafka-storage.sh random-uuid) -c config/server.properties 
   ```

6. Start the Kafka Broker

   ```bash
   bin/kafka-server-start.sh config/server.properties
   ```
   
_**Open separate terminal windows for next steps**_


7. Verify the Cluster

   * Check broker logs: Ensure there are no errors and broker starts correctly and participates in the KRaft quorum.
   * Create a topic: Use the Kafka CLI tool to create a topic and verify it is created across the cluster.
   ```bash
   bin/kafka-topics.sh --create --topic foo --partitions 1 --replication-factor 1 --bootstrap-server localhost:9092
   ```
   * Describe the topic
   ```bash
   bin/kafka-topics.sh --describe --topic foo --bootstrap-server localhost:9092
   ```
8. Run `kafka-console-producer` to produce record to topic `foo`:
   ```bash
   bin/kafka-console-producer.sh --topic foo --bootstrap-server localhost:9092
   ``` 
   Example

   ```bash
   > Hello, Kafka!
   > This is a test message.
   > Another message.
   ``` 
   
9. Run `kafka-console-consumer` to consume record from topic `foo`:
   ```bash
   bin/kafka-console-consumer.sh --topic foo --from-beginning --bootstrap-server localhost:9092
   ```

## Lab 02 : Docker mode {collapsible="true"}

1. Download or copy the contents of the Confluent Platform KRaft Docker Compose file

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

## Lab 03 : Producer {collapsible="true"}

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
   
## Lab 04 : Consumer {collapsible="true"}

* Configure Kafka consumer to ingest sensor data from Kafka topic in real-time.
* Process incoming data
* You will learn how to tweak settings such as `fetch.min.bytes`, `fetch.max.wait.ms`, `max.poll.records` to achieve optimal performance.

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

6. Run the consumer with different configurations for `fetch.min.bytes`, `fetch.max.wait.ms`,and `max.poll.records` to observe their effects on message consumption and performance.
7. Stop kafka cluster stack with -v to remove volumes

   ```bash
   docker-compose down -v
   ```

## Lab 05 : Kafka REST Proxy {collapsible="true"}

1. Start kafka cluster stack with the -d option to run in detached mode:
   ```bash
   docker-compose up -d
   ```
2. Produce messages to a Kafka topic using REST Proxy
   * Create a Kafka topic
   ```bash
   kafka-topics --create --topic bar --bootstrap-server kafka:9092 --partitions 1 --replication-factor 1
   ```
   * Produce a JSON message to the topic
   ```bash
   curl -X POST -H "Content-Type: application/vnd.kafka.json.v2+json" \
   --data '{"records":[{"value":{"name":"toto", "age":30}}]}' \
   http://localhost:8082/topics/bar
   ```
3. Consume messages from a Kafka topic using REST Proxy
   * Create a new consumer instance
   ```bash
   curl -X POST -H "Content-Type: application/vnd.kafka.v2+json" \
   --data '{"name": "my_consumer_instance", "format": "json", "auto.offset.reset": "earliest"}' \
   http://localhost:8082/consumers/my_consumer_group
   ```   
   * Subscribe the consumer to the topic
   ```bash
   curl -X POST -H "Content-Type: application/vnd.kafka.v2+json" \
   --data '{"topics":["bar"]}' \
   http://localhost:8082/consumers/my_consumer_group/instances/my_consumer_instance/subscription
   ```   
   * Consume messages
   ```bash
   curl -X GET -H "Accept: application/vnd.kafka.json.v2+json" \
   http://localhost:8082/consumers/my_consumer_group/instances/my_consumer_instance/records
   ```
4. Commit consumer offsets using REST Proxy
   ```bash
   curl -X POST -H "Content-Type: application/vnd.kafka.v2+json" \
   --data '{"offsets": [{"topic": "bar", "partition": 0, "offset": 10}]}' \
   http://localhost:8082/consumers/my_consumer_group/instances/my_consumer_instance/offsets
   ```
5. Stop kafka cluster stack with -v to remove volumes

   ```bash
   docker-compose down -v
   ```

## Lab 06 : Schema Registry {collapsible="true"}

1. Start kafka cluster stack with the -d option to run in detached mode

   ```bash
   docker-compose up -d
   ```
2. Register the schema

   ```bash
   curl -X POST -H "Content-Type: application/vnd.schemaregistry.v1+json" \
   --data '{"schema": "{\"type\":\"record\",\"name\":\"User\",\"fields\":[{\"name\":\"name\",\"type\":\"string\"},{\"name\":\"age\",\"type\":\"int\"},{\"name\":\"email\",\"type\":[\"null\",\"string\"],\"default\":null}]}"}' \
   http://localhost:8081/subjects/user-value/versions
   ```
3. Produce messages using avro schema

   * Transform your producer **Lab 03** using schema and schema registry API.
   * Add Maven Avro and Schema Registry dependencies

        ```xml
       <!-- Avro dependencies -->
       <dependency>
           <groupId>org.apache.avro</groupId>
           <artifactId>avro</artifactId>
           <version>1.11.1</version>
       </dependency>
       <!-- Schema Registry dependencies -->
       <dependency>
          <groupId>io.confluent</groupId>
          <artifactId>kafka-avro-serializer</artifactId>
          <version>7.6.0</version>
       </dependency>
      ```

   * Create an `src/main/avro` directory and define an Avro schema (user.avsc)

     ```json
        {
       "type": "record",
       "name": "User",
       "namespace": "com.example.avro",
       "fields": [
         {"name": "name", "type": "string"},
         {"name": "age", "type": "int"}
       ]
       }
     ```

   * Add the Avro Maven plugin to your `pom.xml` to generate Java classes from the Avro schema

     ```xml
     <build>
      <plugins>
          <plugin>
              <groupId>org.apache.avro</groupId>
              <artifactId>avro-maven-plugin</artifactId>
              <version>1.11.1</version>
              <executions>
                  <execution>
                      <phase>generate-sources</phase>
                      <goals>
                          <goal>schema</goal>
                      </goals>
                      <configuration>
                          <sourceDirectory>${project.basedir}/src/main/avro</sourceDirectory>
                          <outputDirectory>${project.basedir}/src/main/java</outputDirectory>
                      </configuration>
                  </execution>
              </executions>
          </plugin>
      </plugins>
     </build>
     ```
   
   * Run the Maven command to generate Java classes

        ```bash
        mvn clean compile
        ```
  
   * Complete and run to produce with avro schema
   
     ```Java
          Properties props = new Properties();
          props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
          props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
          props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, KafkaAvroSerializer.class.getName());
          props.put("schema.registry.url", "http://localhost:8081");

          KafkaProducer<String, User> producer = new KafkaProducer<>(props);

          User user = new User("John Doe", 30);

          ProducerRecord<String, User> record = new ProducerRecord<>("users", user.getName().toString(), user);
     ```
     
   * Execute the Producer class and ensure a message is produced to the `users` topic
  
4. Consume messages using the schema

   * Transform your consumer `Lab 04` using schema and schema registry API.
   * Add Maven Avro and Schema Registry dependencies like before
   * Add the Avro Maven plugin to your `pom.xml` to generate Java classes from the Avro schema like before

     ```Java
           Properties props = new Properties();
           props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
           props.put(ConsumerConfig.GROUP_ID_CONFIG, "user-consumer-group");
           props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
           props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, KafkaAvroDeserializer.class.getName());
           props.put("schema.registry.url", "http://localhost:8081");
           props.put(KafkaAvroDeserializerConfig.SPECIFIC_AVRO_READER_CONFIG, true);

           KafkaConsumer<String, User> consumer = new KafkaConsumer<>(props);
     ```
     
   * Execute the Consumer class and verify that the consumer reads the message from the `users` topic.

You can also always email me at [mohamedkaraga@yahoo.fr](mailto:mohamedkaraga@yahoo.fr).

## Lab 07 : Deploy a kafka connect {collapsible="true"}

1. Start kafka cluster stack with the -d option to run in detached mode

   ```bash
   docker-compose up -d
   ```
   
2. Install JDBC Connector

   ```bash
   docker-compose exec -u root connect confluent-hub install confluentinc/kafka-connect-jdbc:10.7.6
   ```
   
3. Restart the connect container

   ```bash
   docker-compose restart connect
   ```
   **Output**
   ```Console
   The component can be installed in any of the following Confluent Platform installations: 
   1. / (installed rpm/deb package)
   2. / (where this tool is installed)
      Choose one of these to continue the installation (1-2): 1
      Do you want to install this into /usr/share/confluent-hub-components? (yN) N
   
   Specify installation directory: /usr/share/java/kafka
   
   Component's license:
   Confluent Community License
   https://www.confluent.io/confluent-community-license
   I agree to the software license agreement (yN) y
   
   Downloading component Kafka Connect JDBC 10.7.6, provided by Confluent, Inc. from Confluent Hub and installing into /usr/share/java/kafka
   Detected Worker's configs:
   1. Standard: /etc/kafka/connect-distributed.properties
   2. Standard: /etc/kafka/connect-standalone.properties
   3. Standard: /etc/schema-registry/connect-avro-distributed.properties
   4. Standard: /etc/schema-registry/connect-avro-standalone.properties
   5. Used by Connect process with PID : /etc/kafka-connect/kafka-connect.properties
      Do you want to update all detected configs? (yN) y
   
   Adding installation directory to plugin path in the following files:
   /etc/kafka/connect-distributed.properties
   /etc/kafka/connect-standalone.properties
   /etc/schema-registry/connect-avro-distributed.properties
   /etc/schema-registry/connect-avro-standalone.properties
   /etc/kafka-connect/kafka-connect.properties
   
   Completed

   ```

4. Access the PostgreSQL Container

   ```bash
   docker-compose exec postgres psql -U myuser -d lab
   ```
5. Inside the PostgreSQL container, create a table named `users` and insert some sample data

   ```SQL
   -- Create the users table
   CREATE TABLE users (
   id SERIAL PRIMARY KEY,
   name VARCHAR(100),
   email VARCHAR(100),
   created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   );
   
   -- Insert sample data into the users table
   INSERT INTO users (name, email) VALUES
   ('toto', 'toto@example.com'),
   ('titi', 'titi@example.com'),
   ('tata', 'tata@example.com');
   ```

6. Create the jdbc-source-config.json file for the JDBC Source connector to pull data from this users table and publish it to a Kafka topic

   ```JSON
   {
   "name":"jdbc-source-connector",
   "config":{
   "connector.class":"io.confluent.connect.jdbc.JdbcSourceConnector",
   "tasks.max":"1",
   "connection.url":"jdbc:postgresql://postgres:5432/lab",
   "connection.user":"myuser",
   "connection.password":"mypassword",
   "table.whitelist":"users",
   "mode":"incrementing",
   "incrementing.column.name":"id",
   "topic.prefix":"postgres-",
   "poll.interval.ms":"10000"
   }
   }
   ```
   
7. Deploy the JDBC Source Connector

   ```bash
   curl -X POST -H "Content-Type: application/json" --data @jdbc-source-config.json http://connect:8083/connectors
   ```
8. Check the status of the JDBC Source connector

   ```bash
   curl -X GET http://connect:8083/connectors/jdbc-source-connector/status
   ```
9. Use Control center to verify the data is being published from the PostgreSQL users table

## Lab 08 : Basic kafka stream {collapsible="true"}

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
6. Advanced : apply windowed aggregation to count the number of vehicles every 1 minute

Transform the Kafka Streams application to use windowed operations,
For this exercise, let's assume you want to use `tumbling` windows to count the number of vehicles within a certain time window 
and generate congestion alerts based on the counts within each window.



## Lab 09 : introduce KsqlDB {collapsible="true"}
