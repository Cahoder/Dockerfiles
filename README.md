1.## centos7-container --install
## build a docker image by Dockerfile
docker build --rm -t centos7-image .

# run a centos-image become a docker container
docker run -itd --name local-centos7 --privileged -v /sys/fs/cgroup:/sys/fs/cgroup:ro centos7-image

# enter a centos7-container
docker exec -it local-centos7 /bin/bash


2.## php7.2-container --install
## build a docker image by Dockerfile
docker build --rm -t php-image .

# run a php-image become a docker container
docker run --name local-php -d -v /var/www:/var/www/html php-image

# use a php-container
docker exec -it local-php CMD [ Argument1,Argument2,...... ]

3.## nginx-container --install
## build a docker image by Dockerfile
docker build --rm -t nginx-image .

# run a nginx-image become a docker container
docker run -d -p 80:80 --name local-nginx -v /var/www:/usr/share/nginx/html -v /var/www/docker/nginx/logs:/var/log/nginx -v /var/www/docker/nginx/nginx.conf:/etc/nginx/nginx.conf:ro -v /var/www/docker/nginx/conf.d:/etc/nginx/conf.d:ro --link local-php:php --link local-redis:redis --link local-memcached:memcached nginx-image

# use a nginx-container
docker exec -it local-nginx CMD [ Argument1,Argument2,...... ]

4.## memcached-container --install
## build a docker image by Dockerfile
docker build --rm -t memcached-image .

# run a memcached-image become a docker container
docker run -d --name local-memcached memcached-image

# use a memcached-container
docker exec -it local-memcached CMD [ Argument1,Argument2,.... ]

5.## redis-container --install
## build a docker image by Dockerfile
docker build --rm -t redis-image .

# run a redis-image become a docker container
docker run -d --name local-redis -v /var/www/docker/redis/data:/data -v /var/www/docker/redis/redis.conf:/etc/redis/redis.conf -d redis-image redis-server /etc/redis/redis.conf

# use a redis-container
docker exec -it local-redis CMD [ Argument1,Argument2,.... ]


### 实用技巧

#CHECK A CONTAINER IP ADDRESS
docker inspect --format '{{ .NetworkSettings.IPAddress }}' < container-id >
