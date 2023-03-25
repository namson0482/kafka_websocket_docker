# Sale Report

### Prerequisite
- You computer need to install Docker

### Setup
- Linux/Mac OS: open file /etc/hosts and add new line
```
    127.0.0.1   websocket
```
- For window, you can find out hosts file in C:\Windows\System32\drivers\etc\hosts
- Now execute a command
```
docker-compose up -d
```
- Open any web browser and go to http://websocket:7070

### Additional
- you can use Kafdop to monitor Kafka, just access http://localhost:9000

DONE

### Enhancement
- Here, we should use Schema Avro to sending/received message over Kafka.
- I only check input data that are valid so source code not yet cover for all cases.
- Not yet check with huge data.
