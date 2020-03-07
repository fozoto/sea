# spark的安装

版本: spark-2.4.5-bin-hadoop2.7

[下载地址](https://mirrors.tuna.tsinghua.edu.cn/apache/spark/spark-2.4.5/spark-2.4.5-bin-hadoop2.7.tgz)

## 安装

1. 解压至/soft/spark-2.4.5-bin-hadoop2.7

2. 设置环境变量

   ```
   export SPARK_HOME=/soft/spark-2.4.5-bin-hadoop2.7 && export $PATH=$PATH:SPARK_HOME/bin:$SPARK_HOME/sbin
   ```

3. 设置spark参数

   ```
   mv /soft/spark-2.4.5-bin-hadoop2.7/conf/slaves.template /soft/spark-2.4.5-bin-hadoop2.7/conf/slaves
   mv /soft/spark-2.4.5-bin-hadoop2.7/conf/spark-env.sh.template /soft/spark-2.4.5-bin-hadoop2.7/conf/spark-env.sh
   echo 'JAVA_HOME=/soft/jdk1.8.0_191' >> /soft/spark-2.4.5-bin-hadoop2.7/conf/spark-env.sh
   echo 'HADOOP_CONF_DIR=/soft/hadoop-2.7.7/etc/hadoop' >> /soft/spark-2.4.5-bin-hadoop2.7/conf/spark-env.sh
   ```

## 使用

### 进入spark-shell

```shell
spark-shell --master spark://localhost:7077
```

