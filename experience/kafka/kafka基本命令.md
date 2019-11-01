# kafka基本命令操作

`version`: 2.11_2.30

列出所有topic

> kafka-topics.sh --bootstrap-server=localhost:9092 --list

列出所有消费组

> kafka-consumer-groups.sh --bootstrap-server=localhost:9092 --list

查看某个消费组的详细信息

> kafka-consumer-groups.sh --bootstrap-server=localhost:9092 --group qingyan --describe

查看某个topic的分区等详细信息

> kafka-topics.sh --bootstrap-server=localhost:9092 --topic topic1 --describe

命令行消费某个topic(--from-topic从头开始消费)

> kafka-console-consumer.sh --bootstrap-server=localhost:9092 --topic topic1 --from-beginning

正则删除多个topic

> kafka-topics.sh --bootstrap-server=localhost:9092 --delete --topic=topic\(.*\)

消息生产者

> kafka-console-producer.sh --broker-list=localhost:9092 --topic topic1

重置某个消费组的offset

> kafka-consumer-groups.sh --bootstrap-server=localhost:9092 --group console-consumer-3127 --reset-offsets --to-earliest --execute --topic topic1
> kafka-console-consumer.sh --bootstrap-server=localhost:9092 --topic topic1 --group console-consumer-3127
