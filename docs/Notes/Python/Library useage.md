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
