FROM centos:latest

USER root
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-* && \
    sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-* && \
    yum update -y && \
    yum install curl

# https://github.com/ufoscout/docker-compose-wait
## Add the wait script to the image
ADD https://github.com/ufoscout/docker-compose-wait/releases/download/2.10.0/wait /wait
RUN chmod +x /wait

WORKDIR /
RUN mkdir -p /temp && \
    mkdir -p /scripts && \
    chmod 755 /temp && \
    chmod 755 /scripts

ADD start.sh /scripts/start.sh
RUN chmod +x /scripts/start.sh

# https://github.com/vishnubob/wait-for-it
COPY wait-for-it.sh /scripts

CMD /wait && /scripts/start.sh
