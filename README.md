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
# 查看所有topic详细信息
./kafka-topics.sh  --describe --bootstrap-server  :9092
./kafka-topics.sh  --describe --zookeeper :2181

# 创建一个topic
./kafka-topics.sh --create --topic w2 --partitions 1 --zookeeper :2181 --replication-factor 3

# 生产者发送消息
./kafka-console-producer.sh --broker-list :9092 --topic w2

# 从头开始消费一个topic
./kafka-console-consumer.sh --bootstrap-server :9092 --topic w2  --from-beginning 

# 显示某一个topic
./kafka-topics.sh  --bootstrap-server xwj-kafka-1.com:9092 --topic jgtaskon --describe

opic: testnew PartitionCount: 3 ReplicationFactor: 3  Configs: segment.bytes=1073741824
  Topic: testnew  Partition: 0  Leader: 1 Replicas: 1,2,3 Isr: 1,2,3
  Topic: testnew  Partition: 1  Leader: 2 Replicas: 2,3,1 Isr: 2,3,1
  Topic: testnew  Partition: 2  Leader: 3 Replicas: 3,1,2 Isr: 3,1,2

Partition表示分区编号，Leader表示主分区ID

# 以列表的形式显示所有topic
./kafka-topics.sh  --bootstrap-server xwj-kafka-1.com:9092 --list

# 修改topic的分区数量（只能增加，不能减小）
./kafka-topics.sh  --bootstrap-server xwj-kafka-1.com:9092 --topic topicname --alter --partitions 30

# 创建一个topic，并动态配置相关属性
./kafka-topics.sh  --bootstrap-server xwj-kafka-1.com:9092 --create --topic xx1 --partitions 3 --replication-factor 2  --config max.message.bytes=64000    

# 动态修改属性，--entity-type表示修改topic，--entity-name是topic名称
./kafka-configs.sh --bootstrap-server xwj-kafka-1.com:9092  --entity-type topics --entity-name topicname --alter --add-config max.message.bytes=128000

# 显示动态设置的相关属性
./kafka-configs.sh  --bootstrap-server xwj-kafka-1.com:9092  --entity-type topics --entity-name topicname --describe

# 查看一个消费组情况，包含GROUP/TOPIC/PARTITION/CURRENT-OFFSET/LOG-END-OFFSET/LAG/CONSUMER-ID/HOST/CLIENT-ID
./kafka-consumer-groups.sh  --bootstrap-server xwj-kafka-qafz-1.com:9092  --describe --group sapp 

# 指定消费组消费
./kafka-console-consumer.sh --bootstrap-server  kafka1:9092 --topic test_partition --consumer-property  group.id=test_group

# 删除一个topic
./kafka-topics.sh --bootstrap-server xwj-kafka-1.com:9092  --topic topicname --delete

# 删除一个消费者组 (确保没有在消费了 Deletion of some consumer groups failed：The group is not empty)
./kafka-consumer-groups.sh --bootstrap-server  xwj-kafka-1.com:9092 --group groupname --delete

# 查看topic大小
kafka-log-dirs.sh \
  --bootstrap-server xwj-kafka-1.com:9092 \
  --topic-list article \
  --describe \
  | grep -oP '(?<=size":)\d+'  \
  | awk '{ sum += $1 } END { print sum }'

# 从尾部开始消费（指定分区）
kafka-console-consumer.sh --bootstrap-server --topic mytopic  --offset latest  --partition 0 1 2 
kafka-console-consumer.sh --bootstrap-server --topic mytopic  --offset latest  --partition 0 1 2 --max-messages 2

# 查看消息内容
$ kafka-dump-log.sh --files cr7-topic-0/00000000000000000000.log  -deep-iteration --print-data-log
```

### 参考

- [conduktor kafka全家桶](https://github.com/conduktor/kafka-stack-docker-compose/)
- [入门](https://developer.confluent.io/quickstart/kafka-docker/)
- [启动失败说明](https://github.com/wurstmeister/kafka-docker/issues/559)
- [启动失败说明](https://stackoverflow.com/questions/59592518/kafka-broker-doesnt-find-cluster-id-and-creates-new-one-after-docker-restart)