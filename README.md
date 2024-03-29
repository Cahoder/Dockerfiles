### Linux + nginx + mysql + php docker file

### 1.## centos7-container --install
## build a docker image by Dockerfile
docker build --rm -t centos7-image .

# run a centos-image become a docker container
docker run -itd --name local-centos7 --privileged -v /sys/fs/cgroup:/sys/fs/cgroup:ro centos7-image

# enter a centos7-container
docker exec -it local-centos7 /bin/bash


### 2.## php7.2-container --install
## build a docker image by Dockerfile
docker build --rm -t php-image .

# run a php-image become a docker container
docker run --name local-php -d -v /var/www:/var/www/html php-image

# use a php-container
docker exec -it local-php CMD [ Argument1,Argument2,...... ]

### 3.## nginx-container --install
## build a docker image by Dockerfile
docker build --rm -t nginx-image .

# run a nginx-image become a docker container
docker run -d -p 80:80 --name local-nginx -v /var/www:/usr/share/nginx/html -v /var/www/docker/nginx/logs:/var/log/nginx -v /var/www/docker/nginx/nginx.conf:/etc/nginx/nginx.conf:ro -v /var/www/docker/nginx/conf.d:/etc/nginx/conf.d:ro --link local-php:php nginx-image

# use a nginx-container
docker exec -it local-nginx CMD [ Argument1,Argument2,...... ]

### 4.## memcached-container --install
## build a docker image by Dockerfile
docker build --rm -t memcached-image .

# run a memcached-image become a docker container
docker run --network container:local-php -d --name local-memcached memcached-image

# use a memcached-container
docker exec -it local-memcached CMD [ Argument1,Argument2,.... ]

### 5.## redis-container --install
## build a docker image by Dockerfile
docker build --rm -t redis-image .

# run a redis-image become a docker container
docker run -d --name local-redis --network container:local-php -v /var/www/docker/redis/data:/data -v /var/www/docker/redis/redis.conf:/etc/redis/redis.conf -d redis-image redis-server /etc/redis/redis.conf

# use a redis-container
docker exec -it local-redis CMD [ Argument1,Argument2,.... ]

### 6.## mysql-container --install
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

#HOW TO USE A COMPOSER
docker exec --privileged -it -w /var/www/html/[ *** ] local-php composer [ Argument1,.... ]

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
