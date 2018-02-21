docker create -v /config --name dataConatiner busybox
docker cp config.conf dataConatiner:/config/
docker run --volumes-from dataContainer ubuntu ls /config
docker run --volumes-from dataContainer ubuntu ls /config
docker export dataContainer > dataContainer.tar
docker export dataContainer > dataContainer.tar



docker import dataContainer.tar
docker run --link redis-server:redis alpine env

docker run --link redis-server:redis alpine ping -c 1 redis
docker run -d -p 3000:3000 --link redis-server:redis katacoda/redis-node-docker-example
docker run -d -p 3000:3000 --link redis-server:redis katacoda/redis-node-docker-example


docker network create backend-network
docker run -d --name=redis --net=backend-network redis

docker run --net=backend-network alpine cat /etc/hosts
docker run --net=backend-network alpine cat /etc/resolv.conf
docker run --net=backend-network alpine ping -c1 redis


docker network create frontend-network
docker network connect frontend-network redis

docker network create frontend-network2
docker network connect --alias db frontend-network2 redis

docker run --net=frontend-network2 alpine ping -c1 db

docker network ls
docker network inspect frontend-network
docker network disconnect frontend-network redis

docker run  -v /docker/redis-data:/data \
  --name r1 -d redis \
  redis-server --appendonly yes
  
  cat data | docker exec -i r1 redis-cli --pipe
  
ls /docker/redis-data
docker run  -v /docker/redis-data:/backup ubuntu ls /backup
docker run --volumes-from r1 -it ubuntu ls /data

docker run -v /docker/redis-data:/data:ro -it ubuntu rm -rf /data

docker run -d --name redis-syslog --log-driver=syslog redis


docker inspect --format '{{ .HostConfig.LogConfig }}' redis-server

docker run -d --name restart-default scrapbook/docker-restart-example

docker logs restart-3

docker inspect -f "{{json .Config.Labels }}" rd
docker inspect -f "{{json .ContainerConfig.Labels }}" katacoda-label-example


echo 'user=123461' >> labels && echo 'role=cache' >> labels
docker run --label-file=labels -d redis
docker ps --filter "label=user=scrapbook"
docker images --filter "label=vendor=Katacoda"

docker -d \
-H unix:///var/run/docker.sock \
--label com.katacoda.environment="production" \
--label com.katacoda.storage="ssd"



docker run -d -p 80:80 -e DEFAULT_HOST=proxy.example -v /var/run/docker.sock:/tmp/docker.sock:ro --name nginx jwilder/nginx-proxy

web:
  build: .

  ports:
    - "3000"
    - "8000"

  links:
    - redis

redis:
  image: redis:alpine
  volumes:
    - /var/redis/data:/data

