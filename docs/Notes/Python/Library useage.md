# Hydra
change the working space directory

```python
# D:\software\miniconda\envs\chronicle\lib\site-packages\hydra\conf\__init__.py
chdir: Optional[bool] = False
```

# Comet.ml

.comet.config
```
[comet]
api_key=QolYCDqLZSGU5ut5E2B2IFlSM
workspace=rambo55555
project_name=cmg
```

# 向量数据库

## Faiss

```python
# pip install faiss-cpu
```



## ElasticSearch

pip install elasticsearch

pip list | grep elasticsearch 8.14.0



docker network create elastic-network

docker pull docker.elastic.co/elasticsearch/elasticsearch:8.14.1

wget https://artifacts.elastic.co/cosign.pub



docker run --name es01 --net elastic-network -p 9200:9200 -it -m 1GB docker.elastic.co/elasticsearch/elasticsearch:8.14.0

docker run --rm --name esn01 -p 9200:9200 -v esdata01:/home/rambo/es/data --network elastic-network -e "node.name=esn01" -e "cluster.name=liuxg-docker-cluster" -e "cluster.initial_master_nodes=esn01" -e "bootstrap.memory_lock=true" --ulimit memlock=-1:-1 -e ES_JAVA_OPTS="-Xms512m -Xmx512m" docker.elastic.co/elasticsearch/elasticsearch:8.14.0



docker run -d \
--restart=always \
--name es \
--network es-net \
-p 9200:9200 \
-p 9300:9300 \
--privileged \
-v /usr/local/es/data:/usr/share/elasticsearch/data \
-v /usr/local/es/plugins:/usr/share/elasticsearch/plugins \
-e "discovery.type=single-node" \
-e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
elasticsearch:8.7.0



解决启动问题
https://blog.csdn.net/hhl18730252820/article/details/135462929

```
version: '3'
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.14.0
    container_name: esn01
    environment:
      #- node.name=esn01
      #- cluster.name=liuxg-docker-cluster
      #- cluster.initial_master_nodes=esn01
      #- bootstrap.memory_lock=true
      - discovery.type=single-node
      - xpack.security.enabled=false
      # - xpack.security.enrollment.enabled=true
      # - xpack.security.autoconfiguration.enabled=true
      - ES_JAVA_OPTS=-Xms4G -Xmx4G
    # ulimits:
    #   memlock:
    #     soft: -1
    #     hard: -1
    volumes:
      - esdata01:/home/rambo/es/data
    ports:
      - 9200:9200
    networks:
      - elastic

  kibana:
    image: docker.elastic.co/kibana/kibana:8.14.0
    container_name: kibana
    environment:
      - ELASTICSEARCH_HOSTS=http://localhost:9200
    depends_on: 
      - elasticsearch
    ports:
      - 5601:5601
    networks:
      - elastic

networks:
  elastic:
    name: elastic
    driver: bridge

volumes:
  esdata01:

```

安装：https://elasticstack.blog.csdn.net/article/details/122874932
