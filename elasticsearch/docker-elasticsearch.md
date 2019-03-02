# Docker 

- reference : https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html
- ES 레퍼런스에 나온대로 docker-compose를 설정하면 `bootstrap checks failed` 발생
  1. max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536] 
  > ~~~yml
  > docker-compose.yml
  >   nofile:
  >   soft: 65536
  >   hard: 65536
  > ~~~ 
  2. max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
  > ~~~bash
  > # vm.max_map_count 추가
  > sudo vi /etc/sysctl.conf
  > vm.max_map_count = 262144 
  > sudo sysctl -p 
  > ~~~
- docker-compose.yml에서 data, config 디렉토리 볼륨설정
  - 볼륨 마운트 위치 확인 : `docker volume inspect elasticsearch_esdata`
  ~~~bash
  $ docker volume inspect elasticsearch_esdata

    [
        {
            "CreatedAt": "2019-02-14T00:24:14+09:00",
            "Driver": "local",
            "Labels": {
                "com.docker.compose.project": "elasticsearch",
                "com.docker.compose.version": "1.23.2",
                "com.docker.compose.volume": "esdata"
            },
            "Mountpoint": "/var/lib/docker/volumes/elasticsearch_esdata/_data",
            "Name": "elasticsearch_esdata",
            "Options": null,
            "Scope": "local"
        }
    ]
  ~~~


~~~yml
docker-compose.yml

version: '2.2'
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:6.5.1
    container_name: elasticsearch
    environment:
      - cluster.name=docker-cluster
      - node.name=docker-node1
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms2g -Xmx2g"
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    volumes:
      - esdata:/usr/share/elasticsearch/data
      - esconfig:/usr/share/elasticsearch/config
    ports:
      - 9200:9200
      - 9300:9300
    networks:
      - esnet

volumes:
  esdata:
    driver: local
  esconfig:
    driver: local

networks:
  esnet:
~~~