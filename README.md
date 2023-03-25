# Sale Report

This docker will run Kafka and Websocket to show Sale Report. It already tested on 4 computer as MAC OS (Chip Intel), Window 10, Linux passed and it is failure on MAC OS (Chip M1)

### Prerequisite
- Your computer must install Docker in advance.
- Your computer must get port number as **2181, 9000, 8080, 9092, 29092, 7070** and **8082** that are available.
- All containers must run one computer because I only test this case.

### Setup
- Linux/Mac OS: open file /etc/hosts and add new line:
```
    127.0.0.1   websocket
```
- For window, you can find out hosts file in C:\Windows\System32\drivers\etc\hosts
- Now execute a command:
```
docker-compose up -d
```
- Open any web browser and go to http://localhost:4200. If you get a error so it just refresh your browser. Root cause maybe that containers not yet started completely, If you use a other computer to access then you maybe get a errors CORS
- As requirement, it needs to archive the raw files in other place. You can ssh into container and find them in folder /temp. If you want to mount local folder with /temp of container then you just uncomment configuration in docker-compose.yml

### Additional
- you can use Kafdop to monitor Kafka, just access http://localhost:9000

**DONE**

### Enhancement
- Here, we should use Schema Avro to sending/received message over Kafka. Currently, I sent/received data over Apache Kafka by String and it is not good. 
- I only check input data that are valid so source code not yet cover for all cases.
- Not yet check with huge data.

### Source Code Link
