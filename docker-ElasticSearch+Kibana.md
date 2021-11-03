# ElasticSearch+Kibana

### 安装ElasticSearch

[ElasticSearch下载链接](https://hub.docker.com/_/elasticsearch)

[Kibana下载链接](https://hub.docker.com/_/kibana)

**两个得版本必须对应的,不然可能kibana访问不到es**

```dockerfile
docker pull elasticsearch
```

```dockerfile
docker pull kibana
```

#### 创建自定义网络(用于连接到同一网络的其他网络)

```dockerfile
docker network create somenetwork
```

#### 运行es

```dockerfile
docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.1.1
```

#### 测试是否运行成功

```
访问 127.0.0.1:9200
```



#### 运行kibana

```dockerfile
docker run -d --name kibana --net somenetwork -p 5601:5601 -e ELASTICSEARCH_URL=http://localhost:9200 kibana:7.1.1
```

#### 测试是否运行成功

```
访问 http://127.0.0.1:5601  （启动可能会较慢，如失败等几秒再尝试刷新一下）
```

修改kibana的语言

```
##进入容器
docker exec -it Kibana容器id bash
##进入config目录
cd config/
##编辑kibana.yml文件
vi kibana.yml 
##添加
i18n.locale: "zh-CN"
##保存文件然后重启kibana
```

