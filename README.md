### 服务信息

1：使用docker安装zookeeper:6.2.1，kafka:6.2.1

2：三节点kafka，单节点zookeeper

3：zoonavigator，kafka控制台

4：设置host：

```
主机IP  xwj-kafka-1.com
主机IP  xwj-kafka-2.com
主机IP  xwj-kafka-3.com
```

配置 zoonavigator

```
server {
  location / {
     proxy_pass http://主机IP:9095;
  }
}
```

### 安装说明 

持久化：

```
chmod 0777 kafka1data
chmod 0777 kafka2data
chmod 0777 kafka3data
chmod 0777 zkdata
chmod 0777 zklog
```

如果想让非本主机访问，设置:

```
export DOCKER_HOST_IP=主机IP
```

启动：

```
docker-compose build
docker-compose up
docker-compose down
```

### 使用参考

```
./kafka-topics.sh  --describe --bootstrap-server  :9092

./kafka-topics.sh  --describe --zookeeper :2181

./kafka-topics.sh --create --topic w2 --partitions 1 --zookeeper :2181 --replication-factor 3

./kafka-console-producer.sh --broker-list :9092 --topic w2
 
./kafka-console-consumer.sh --bootstrap-server :9092 --topic w2  --from-beginning 

./kafka-topics.sh  --bootstrap-server xwj-kafka-1.com:9092 --topic order-union-qa --describe

./kafka-topics.sh  --bootstrap-server xwj-kafka-1.com:9092 --list

./kafka-topics.sh  --bootstrap-server xwj-kafka-1.com:9092 --create --topic xx1 --partitions 3 --replication-factor 2  --config max.message.bytes=64000    

./kafka-configs.sh --bootstrap-server xwj-kafka-1.com:9092  --entity-type topics --entity-name xx1 --alter --add-config max.message.bytes=128000

#显示动态设置的配置
./kafka-configs.sh  --bootstrap-server xwj-kafka-1.com:9092  --entity-type topics --entity-name xx1 --describe

#查看一个消费组情况
./kafka-consumer-groups.sh  --bootstrap-server xwj-kafka-qafz-1.com:9092  --describe --group article-score 
```

 

### 参考

- [conduktor kafka全家桶](https://github.com/conduktor/kafka-stack-docker-compose/)
- [入门](https://developer.confluent.io/quickstart/kafka-docker/)
- [启动失败说明](https://github.com/wurstmeister/kafka-docker/issues/559)
- [启动失败说明](https://stackoverflow.com/questions/59592518/kafka-broker-doesnt-find-cluster-id-and-creates-new-one-after-docker-restart)