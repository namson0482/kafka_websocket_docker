# docker-compose-wait

A small command-line utility to wait for other docker images to be started while using docker-compose.
It permits waiting for:
- a fixed amount of seconds
- until a TCP port is open on a target image
- until a file or directory is present on the local filesystem

## Usage

This utility should be used in the docker build process and launched before your application starts.

For example, your application "MySuperApp" uses MongoDB, Postgres and MySql (wow!) and you want to be sure that, when it starts, all other systems are available, then simply customize your dockerfile this way:
```dockerfile
## Use whatever base image
FROM centos:latest

# https://github.com/ufoscout/docker-compose-wait
## Add the wait script to the image
ADD https://github.com/ufoscout/docker-compose-wait/releases/download/2.10.0/wait /wait
RUN chmod +x /wait

WORKDIR /
RUN mkdir -p /temp && \
    mkdir -p /scripts && \
    chmod 755 /temp && \
    chmod 755 /scripts

## Add your application to the docker image
ADD start.sh /scripts/start.sh
RUN chmod +x /scripts/start.sh

# https://github.com/vishnubob/wait-for-it
# you want to use a commmand in docker-compose.yml file so please add wait-for-it.sh file
COPY wait-for-it.sh /scripts

## Launch the wait tool and then your application
CMD /wait && /scripts/start.sh
```
Done! the image is ready.

Now let's modify the docker-compose.yml file:

Solution 1: 
```yml
version: "3"

services:
  mongo:
    image: mongo:3.4
    hostname: mongo
    ports:
      - "27017:27017"

  postgres:
    image: "postgres:9.4"
    hostname: postgres
    ports:
      - "5432:5432"

  mysql:
    image: "mysql:5.7"
    hostname: mysql
    ports:
      - "3306:3306"

  mySuperApp:
    image: "mySuperApp:latest"
    hostname: mySuperApp
    environment:
      WAIT_HOSTS: postgres:5432, mysql:3306, mongo:27017
```
Solution 2:
```yml
version: "3"

services:
  mongo:
    image: mongo:3.4
    hostname: mongo
    ports:
      - "27017:27017"

  postgres:
    image: "postgres:9.4"
    hostname: postgres
    ports:
      - "5432:5432"

  mysql:
    image: "mysql:5.7"
    hostname: mysql
    ports:
      - "3306:3306"

  mySuperApp:
    image: "mySuperApp:latest"
    hostname: mySuperApp
    command: bash -c "/scripts/wait-for-it.sh -t 20 hostname:5432 && /scripts/wait-for-it.sh -t 20 mysql:3306 && java -jar /kafka-websocket-producer-0.0.1.jar"
```
When docker-compose is started (or Kubernetes or docker stack or whatever), your application will be started only when all the pairs host:port in the WAIT_HOSTS variable are available.
The WAIT_HOSTS environment variable is not mandatory, if not declared, the script executes without waiting.

If you want to use the script directly in docker-compose.yml instead of the Dockerfile, please note that the `command:` configuration option is limited to a single command so you should wrap in a `sh` call. For example:

```bash
command: sh -c "/wait && /MySuperApp.sh"
```

## Additional configuration options

The behaviour of the wait utility can be configured with the following environment variables:

- _WAIT_LOGGER_LEVEL_ : the output logger level. Valid values are: _debug_, _info_, _error_, _off_. the default is _debug_.
- _WAIT_HOSTS_: comma-separated list of pairs host:port for which you want to wait.
- _WAIT_PATHS_: comma-separated list of paths (i.e. files or directories) on the local filesystem for which you want to wait until they exist.
- _WAIT_COMMAND_: command and arguments to run once waiting completes. The invoked command will completely replace the `wait` process. The default is none.
- _WAIT_TIMEOUT_: max number of seconds to wait for all the hosts/paths to be available before failure. The default is 30 seconds.
- _WAIT_HOST_CONNECT_TIMEOUT_: The timeout of a single TCP connection to a remote host before attempting a new connection. The default is 5 seconds.
- _WAIT_BEFORE_: number of seconds to wait (sleep) before start checking for the hosts/paths availability
- _WAIT_AFTER_: number of seconds to wait (sleep) once all the hosts/paths are available
- _WAIT_SLEEP_INTERVAL_: number of seconds to sleep between retries. The default is 1 second.
