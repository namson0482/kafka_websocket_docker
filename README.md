# DEMAND PLANING DEVELOPMENT TASK
INTRODUCTION: WELCOME TO THE VNET SOLUTIONS.
Sales manager Tom wants to aggregate the company's sales data.  Every day, he receives a lot of sales files from a difficult boss. People always want to see aggregated data as soon as possible.  Tom wants a web application that could help him aggregate data and display it in real time.
Can you help him?

### Tom's initial requirements were the following 
We will create a producer java application, a consumer java application, and a website frontend.
A Producer Java application: we constantly receive sales files within one day. For example, we have 3 files contain sales data of October, November, December. We want the application to loop for those 3 files in sequence every minute. After reading file, the application will aggregate the sales data by product, store to calculate the total sales units and the total sales revenue and then publish the result to the event platform (e.g. Kafka) and archive the raw files in another folder.

### Overall
- This docker will run Kafka and Websocket to show Sale Report. 
- It already tested on 5 computers: 
  + MACOS Ventura(Chip Intel) passed.
  + Window 10 passed.
  + Linux Ubuntu 22.04 passed.
  + MACOS Ventura(Chip M1): 1 computer passed and other one failed. Root cause not yet investigated.
- Please view my video clip in this repository. File name is sale_report_720.mov

### Services
- Websocket Producer Service: read psv file and produce messages.
- Websocket Consumer Service: consume messages and write raws data to file.
- Websocket frontend: Display sale report.
- Kafdrop Container: kafka management.
- Schema-registry Container: define the structure of data.
- Control-Center Container: kafka management.
- Kafka is Message Broker.
- Zookeeper.

### Prerequisites
- The computer must install Docker in advance.
- The computer must get port number as **2181, 9092, 29092, 9000, 8082, 8081, 8080, 9021** and **4200** that are available.
- All containers must run on same computer because I only test this case. Other cases not yet test.

### Setup
- Linux/Mac OS: open file /etc/hosts and add new line:
```
127.0.0.1   websocket
```
- For window, you can find out the hosts file in C:\Windows\System32\drivers\etc\hosts
- Now execute a command as per below, and it takes in a while to start all services:
```
docker-compose up -d
```
- Make sure all services that started completely then open any web browser and go to http://localhost:4200. If you get a error so it just refresh your browser. Root cause maybe that containers not yet started completely, If you use a other computer to access then you maybe get a errors CORS
- As requirement, it needs to archive the raw files in other place. You can ssh into container consumer and find them in folder /temp. If you want to mount local folder to /temp folder of container consumer then you just uncomment configuration in docker-compose.yml of service websocket_consumer.

### Additional
- you can use either Kafdop or Control-Center to monitor Kafka, just access http://localhost:9000 or http://localhost:9021
- websocket_producer service: INTERVAL_TIME: Change interval time to read data file, default 10 second. As requirement is 60 second. 

### Deploy CI-CD on AWS
- Now we get all Dockerfile to build docker images. We need to go to Amazon Elastic Container Service (ECS) and choose Amazon ECR to create new repositories at there.
- Once we created a new repository at ECR then we will get docker credential to login. So now we just build the image and push to there.
- Next, create a new cluster. It is my server that will run our applications
- Next, create a new task,
- Back to our cluster to specify our task.
- There are multiple ways that we can expose our application. Above it is one out of way to do that.

### Enhancement
- Here, we should use the Schema Registry server to manage schemas and using Avro/Protobuf/JSON to serialization/deserialization to sending/received message over Kafka. Currently, I sent/received data over Apache Kafka by String and it is not good. 
- Current version, data input must be valid so if data input is invalid then it will throw the exception.
- Not yet check with huge data.
- To improve security for Apache Kafka then we can apply the authorization as Kerberos, OAuth 2... for Apache Kafka

### Source Code Link
- https://github.com/namson0482/websocket_kafka

### Document Reference
- [Kafka cooperate registry server to serialize/deserialize data following Avro/Protobuf/JSON](https://github.com/namson0482/kafka-oauth2)
- [Kafka config OAuth 2](https://github.com/namson0482/kafka-oauth2)
