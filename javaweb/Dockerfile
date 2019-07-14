# This dockerfile uses the centos-7 image
# VERSION 1 - EDITION 1
# Author: docker_user
# Command format: Instruction [arguments / command ] ..

# Base image to use, this nust be set as the first line
FROM centos:7
MAINTAINER cahoder@docker.com
ENV container docker
RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == \
systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/multi-user.target.wants/*;\
rm -f /etc/systemd/system/*.wants/*;\
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*;
VOLUME [ "/sys/fs/cgroup" ]
# if you don't have a *.tar.gz in the host to download it !
#RUN cd /tmp \ 
    #&& wget -c http://mirror.bit.edu.cn/apache/tomcat/tomcat-8/v8.5.43/bin/apache-tomcat-8.5.43.tar.gz -O apache-tomcat8.tar.gz \
    #&& wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u72-b15/jdk-8u72-linux-x64.tar.gz" -O jdk8-linux-x64.tar.gz

# else copy host tar.gz into image
COPY $PWD/apache-tomcat8.tar.gz jdk8-linux-64.tar.gz /tmp/

RUN cd /tmp/ \ 
    && tar -zxvf apache-tomcat8.tar.gz \
    && mv apache-tomcat-8.* /usr/local/src/tomcat8 \
    && touch /etc/profile.d/tomcat.sh \
    && echo -e "\n#TOMCAT ENV \nexport TOMCAT=/usr/local/src/tomcat8 \nexport PATH=\$TOMCAT/bin:\$PATH" >> /etc/profile.d/tomcat.sh \
    && tar -zxvf jdk8-linux-64.tar.gz \
    && mv jdk1.8.* /usr/local/src/jdk8 \ 
    && touch /etc/profile.d/jdk.sh \
    && echo -e "\n#JAVA ENV \nexport JAVA_HOME=/usr/local/src/jdk8 \nexport JRE_HOME=\$JAVA_HOME/jre \nexport PATH=\$PATH:\$JAVA_HOME/bin:\$JRE_HOME/bin \nexport CLASSPATH=.:\$JAVA_HOME/jre/lib/rt.jar:\$JAVA_HOME/lib/dt.jar:\$JAVA_HOME/lib/tools.jar:\$JRE_HOME/lib" >> /etc/profile.d/jdk.sh \
    && rm /tmp/apache-tomcat* \
    && rm /tmp/jdk*
    
ENV JAVA_HOME /usr/local/src/jdk8
ENV PATH $JAVA_HOME/bin:$PATH

ENTRYPOINT [ "/usr/local/src/tomcat8/bin/catalina.sh", "run" ]
