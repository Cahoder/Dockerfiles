# This dockerfile uses the mysql:5.6 image
# VERSION 1 - EDITION 1
# Author: docker_user
# Command format: Instruction [arguments / command ] ..

# Base image to use, this nust be set as the first line
FROM mysql:5.6
MAINTAINER fandow@docker.com

# add an group and user by root
USER root
RUN egrep "^mysql" /etc/passwd; \
    if [ $? -eq 1 ]; \
    then groupadd -r mysql && useradd -r -g mysql mysql; \
    fi

# set timezone
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 3306
CMD ["mysqld"]
