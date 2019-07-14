### 1.## javaweb-container --install
## build a docker image by Dockerfile
docker build --rm -t javaweb-image .

# run a javaweb-image become a docker container
docker run -p 8080:8080 -itd --name local-javaweb --privileged -v /sys/fs/cgroup:/sys/fs/cgroup:ro javaweb-image

# enter a javaweb-container
docker exec -it local-javaweb /bin/bash

### 2.## mysql-container --install
## build a mysql image by Dockerfile
docker build --rm -t mysql-image .

# run a mysql-image become a docker container
docker run -d --name local-mysql -v /var/www/docker/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw mysql-image

# connect to mysql from the mysql command line client
docker run -it --rm mysql-image mysql -h $(docker inspect --format '{{ .NetworkSettings.IPAddress }}' local-mysql) -uroot -p123456

#creating db dumps
docker exec local-mysql sh -c 'exec mysqldump --all-databases -uroot -p"$MYSQL_ROOT_PASSWORD"' > /some/path/on/your/host/all-databases.sql

#restoring data from dump files
docker exec -i local-mysql sh -c 'exec mysql -uroot -p"$MYSQL_ROOT_PASSWORD"' < /some/path/on/your/host/all-databases.sql

### 实用技巧

#CHECK A CONTAINER IP ADDRESS
docker inspect --format '{{ .NetworkSettings.IPAddress }}' < container-id >

#OBTAIN ALL THE COMTAINERS ID
docker ps -aq

#SEE A AVAILABLE OPTIONS
docker run -it --rm < your-image > --verbose --help

#RESTART NETWORKING 
/etc/init.d/networking restart

#UPDATE|COMMIT A CONTAINER TO A IMAGE
docker commit -a "author" -m "describe" local-container container-image:tag

#COPY A RUNNING CONTAINER INNER FILE TO LOCAL
docker cp local-running-container:/inner/path/ /to/local/path
