拉取镜像
```
# 1. 获取clash的镜像
docker pull dreamacro/clash

# 2. 获取WebUI的镜像
docker pull haishanh/yacd
```

mkdir clash
cd clash
wget 'clash订阅地址' -O config.yaml（得把`external-controller: '127.0.0.1:9090'`改成`external-controller: '0.0.0.0:9090'`）

创建docker-compose.yml
```
version: '3.7'
services:
    clash:
        image: dreamacro/clash
        restart: always
        volumes:
            - ./config.yaml:/root/.config/clash/config.yaml
        container_name: clash
        ports:
          - "7890:7890/tcp"
          - "7890:7890/udp"
          - "9090:9090"
    clash-dashboard:
        image: centralx/clash-dashboard
        container_name: clash-dashboard
        ports:
          - "7880:80"
        restart: always
```

启动
```
docker-compose up -d
```
关闭
```
docker-compose stop
```
日志
```
docker-compose logs
```
clash-dashboard地址：`http://gpu6.csubot.cn:7880/`

使用
```
export http_proxy='http://localhost:7890'
export https_proxy='http://localhost:7890'
source /etc/profile

# python
import os
os.environ["http_proxy"] = f"http://127.0.0.1:7890"
os.environ["https_proxy"] = f"http://127.0.0.1:7890"
```
