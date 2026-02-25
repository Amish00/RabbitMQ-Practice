# 🐰 RabbitMQ Practice with Spring Boot

This is a simple RabbitMQ practice project built using **Spring Boot** and **Spring AMQP**.
It demonstrates how to:

* Send and receive **String messages**
* Send and receive **JSON messages**
* Configure Queues, Exchanges, and Bindings
* Use `RabbitTemplate`
* Use `@RabbitListener`
* Configure `Jackson2JsonMessageConverter`

---

## 🚀 Technologies Used

* Java 17+
* Spring Boot
* Spring AMQP
* RabbitMQ
* Lombok
* Maven

---

## 📦 Dependencies

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
```

---

## ⚙️ Application Properties

```properties
spring.application.name=RabbitMq

spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest

rabbitmq.queue.name=practices
rabbitmq.exchange.name=practices_exchange
rabbitmq.routing.key=practices_routing_key

rabbitmq.queue.json.name=json-queue
rabbitmq.routing.json.key=json-routing-key
```

---

## 🏗 RabbitMQ Architecture

### Exchange

* **Name:** `practices_exchange`
* **Type:** Topic Exchange

### Queues

1. `practices` → For String messages
2. `json-queue` → For JSON messages

### Bindings

| Queue      | Routing Key           |
| ---------- | --------------------- |
| practices  | practices_routing_key |
| json-queue | json-routing-key      |

---

## 🔁 Flow of Application

### 1️⃣ String Message Flow

Controller → Producer → Exchange → Queue → Consumer

* Endpoint:

```
GET /api/v1/publish?message=Hello
```

* Producer sends message using:

```java
rabbitTemplate.convertAndSend(exchange, routingKey, message);
```

* Consumer listens using:

```java
@RabbitListener(queues = "${rabbitmq.queue.name}")
```

---

### 2️⃣ JSON Message Flow

Controller → JSON Producer → Exchange → JSON Queue → JSON Consumer

* Endpoint:

```
POST /api/v1/publish
```

* Request Body:

```json
{
  "id": 1,
  "firstName": "John",
  "lastName": "Doe"
}
```

* JSON conversion handled by:

```java
Jackson2JsonMessageConverter
```

* Consumer receives:

```java
@RabbitListener(queues = "${rabbitmq.queue.json.name}")
```

---

## 🧠 Key Components

### 🔹 RabbitMQConfig

* Creates:

  * Queues
  * Topic Exchange
  * Bindings
  * Message Converter
  * RabbitTemplate Bean

### 🔹 Producers

* `RabbitMQProducer` → Sends String message
* `RabbitMQJsonProducer` → Sends JSON message

### 🔹 Consumers

* `RabbitMQConsumer` → Receives String message
* `RabbitMQJsonConsumer` → Receives JSON message

### 🔹 DTO

```java
@Data
public class User {
    private int id;
    private String firstName;
    private String lastName;
}
```

---

## ▶️ How to Run

### 1️⃣ Start RabbitMQ

If installed locally:

```bash
rabbitmq-server
```

Or using Docker:

```bash
docker run -d --hostname rabbit-host --name rabbitmq \
-p 5672:5672 -p 15672:15672 rabbitmq:3-management
```

Management UI:

```
http://localhost:15672
```

Username: `guest`
Password: `guest`

---

### 2️⃣ Run Spring Boot Application

```bash
mvn spring-boot:run
```

---

## 🧪 Testing Endpoints

### Send String Message

```
GET http://localhost:8080/api/v1/publish?message=HelloRabbit
```

### Send JSON Message

```
POST http://localhost:8080/api/v1/publish
Content-Type: application/json
```

Body:

```json
{
  "id": 10,
  "firstName": "Ram",
  "lastName": "Sharma"
}
```

---

## 📌 What I Learned

* How RabbitMQ works with Exchange → Routing Key → Queue
* Difference between simple message and JSON message
* How `@RabbitListener` works internally
* How Spring Boot auto-configures RabbitMQ
* How to configure `Jackson2JsonMessageConverter`
* How to bind multiple queues to one exchange

---

