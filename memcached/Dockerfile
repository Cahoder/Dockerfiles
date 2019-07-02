# This dockerfile uses the memcache:1.5 image
# VERSION 1 - EDITION 1
# Author: docker_user
# Command format: Instruction [arguments / command ] ..

# Base image to use, this nust be set as the first line
FROM memcached:1.5
USER root
MAINTAINER fandow@docker.com
ENV container docker

# set timezone
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

ENTRYPOINT [ "memcached" ]

EXPOSE 11211
CMD ["-u","root","-m","128"]

