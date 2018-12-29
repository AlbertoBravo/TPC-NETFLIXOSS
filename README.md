#### All this material belongs originally to https://github.com/srast3, that is only a fork to complete my personal project and for add some improvements to it.

# spring-kafka-microservice
This is a repository for building a Spring Boot microservice using NetflixOSS and Apache Kafka. 

#### Prerequisite
- Java 1.8
- Kafka
- Zookeeper
- Docker 

### Apache Kafka and Zookeeper with Docker
- Run
  - docker network create kafka-net
  - docker run -d --name zookeeper --network kafka-net zookeeper:latest
  - docker run -d --name kafka --network kafka-net --env ZOOKEEPER_IP=zookeeper ches/kafka
  - docker run --rm --network kafka-net ches/kafka \kafka-topics.sh --create --topic USER_CREATED_TOPIC --replication-factor 1 --partitions 1 --zookeeper zookeeper:2181
  - docker run --rm --interactive --network kafka-net ches/kafka \kafka-console-producer.sh --topic USER_CREATED_TOPIC --broker-list kafka:9092
  - docker run --rm --network kafka-net ches/kafka \kafka-console-consumer.sh --topic USER_CREATED_TOPIC --from-beginning --bootstrap-server kafka:9092

#### Service Registry (Eureka)
- Build/Run
  - mvn clean install
  - java –jar target/EurekaServer-0.0.1-SNAPSHOT.jar
- Check
  - http://localhost:8761/
  
#### Config Server
  - Update properties 
    - SET **search-paths & git-url** in /spring-kafka-microservice/ConfigServer/src/main/resources/application.yml
    - SET **USERNAME** in spring-kafka-microservice/EmailServiceConfigProperties/email-service/dev/email-service.yml
    - SET **PASSWORD** in spring-kafka-microservice/EmailServiceConfigProperties/email-service/dev/email-service.yml
  - Build/Run
    - mvn clean install
    - java –jar target/ConfigServer-0.0.1-SNAPSHOT.jar
- Check
    - http://localhost:8888/email-service/dev

#### User Service
- Run
  - mvn clean install
  - java -jar target/UserAccount-0.0.1-SNAPSHOT.jar
- Check
  - http://localhost:8081/member -- expect 0 records returned

#### Email service
- Run
  - mvn clean install
  - java -jar target/EmailService-0.0.1-SNAPSHOT.jar
  
##### Apache Kafka and Zookeeper with Docker (Update)
- Run
  - docker run -d --name kafka --network kafka-net --publish 9092:9092 --publish 7203:7203 --env KAFKA_ADVERTISED_HOST_NAME=localhost --env ZOOKEEPER_IP=zookeeper ches/kafka

  
#### Test Microservice
Once each micro service is setup and started correctly, you can test the complete flow by
1. Create a new user by calling url – POST http://localhost:8081/register
2. Verify that the new user is created.
3. You can also verify the user by calling – GET http://localhost:8081/member 
4. Verify that registration success email was received at your email address

