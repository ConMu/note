##### 常用命令集（windows）

```bash
## 命令在.\bin\windows目录下执行 执行前缀 .\
$ kafka-server-start.bat .\config\server.properties  # 处于安装目录运行kafka，默认端口
$ kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic topic001 # windows目录下创建topic `topic001`
$ kafka-console-producer.bat --broker-list localhost:9092 --topic topic001 # 创建生产者，指定topic
$ kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic topic001 --from-beginning # 创建消费者，指定topic
$ kafka-topics.bat --list --zookeeper localhost:2181 # 查看topic情况
$ kafka-topics.bat --describe --zookeeper localhost:2181 --topic topic001 # 查看指定topic详情
```

