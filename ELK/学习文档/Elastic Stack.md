# Elastic Stack

ELK：Elastic search、Logstash、Kibana、Beats

Beats：

- FileBeat：日志文件
- PacketBeat：网络流量
- MetricBeat：服务指标

## Elastic Search

### 安装

#### 1、Docker安装

```shell
#拉取镜像
docker pull elasticsearch
#启动镜像
docker run -d -p 9200:9200 -p 9300:9300 --name=es elasticsearch
```

浏览器访问：http://ip:9200

出现一下内容则说明安装成功：

```json
{
  "name" : "SyxSTB3",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "vG6x2jdhQGytQ43lw_mGrg",
  "version" : {
    "number" : "5.6.16",
    "build_hash" : "3a740d1",
    "build_date" : "2019-03-13T15:33:36.565Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}　　
```

#### 2、离线安装

上传tar包，若没有rz命令，则安装

```shell
yum -y install lrzsz
```

```shell
#解压
tar -zxvf elasticsearch-7.7.0-linux-x86_64.tar.gz /usr/local/elasticsearch
```

#### 3、安装elasticsearch-head

```shell
#拉取镜像
docker pull mobz/elasticsearch-head
#运行镜像
docker run -d -p 9100:9100 mobz/elasticsearch-head:5
```

跨域问题需要修改elasticsearch.yml

```sh
vim elasticsearch.yml
#添加
http.cors.enabled: true
http.cors.allow-origin: "*"
```

#### 4、基本概念

- 索引：index，是对逻辑数据的逻辑存储。可以看作关系型数据库的表。Elastic search可以把索引放在一台机器或者分散在多台服务器上，每个索引有一个或者多个分片(shard)，每个分片可以有多个副本。
- 文档：document，存储在ES 中的主要实体。类似与关系型数据库中，表的一行记录。文档由多个字段组成，每个字段可多次出现在同一个文档里，这种字段叫多值字段。
- 映射：mapping，所有文档写进索引前都会进行分析，如何将输入的文本分割为词条、哪些词条又会被过滤，这种行为称为映射。一般由用户自己定义。
- 文档类型：一个索引对象可以存储很多不同用途的对象。每个文档可以由不同的结构。不同的文档类型不能为相同的属性设置不同的类型。

#### 5、RESTful API

##### 创建非结构化索引

```shell
#创建索引
PUT /maldosia

{
	"settings":{
		"index":{
			"number_of_shards": "2", #分片数
			"number_of_replicas": "0" #副本数
		}
	}
}

#删除索引
DELETE /maldosia

{
	"acknowledged": true
}
```

##### 插入数据

URL规则：

POST /{索引}/{类型}/{id}

```shell
POST /maldosia/user/1001

{
	"id": 1001,
	"name":"maldosia",
	"age": 23,
	"sex": "男"
}
```

删除数据：删除一个文档并不会立即从磁盘上移除，它只是被标记已删除。ES将会在你之后添加更多索引的时候在后台进行删除内容的清理。

```shell
DELETE /maldosia/user/1001
```





















