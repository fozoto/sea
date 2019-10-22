# 安装escheduler-1.1.0-backend

版本: 1.1.0

## 环境依赖

1. 依赖zookeeper
2. pip install kazoo
3. 当前用户可以免密登录, 不需要sudo权限

## 安装包准备

```shell
cd /app
wget https://github.com/apache/incubator-dolphinscheduler/releases/download/1.1.0/escheduler-1.1.0-backend.tar.gz
mkdir /app/escheduler-1.1.0-backend
tar -zxvf /app/escheduler-1.1.0-backend.tar.gz -C  /app/escheduler-1.1.0-backend
```

## mysql环境准备

```sql
CREATE DATABASE escheduler DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
GRANT ALL PRIVILEGES ON escheduler.* TO 'escheduler'@'%' IDENTIFIED BY 'escheduler';
GRANT ALL PRIVILEGES ON escheduler.* TO 'escheduler'@'localhost' IDENTIFIED BY 'escheduler';
flush privileges;
```

## 创建表和导入基础数据

> vim /app/escheduler-1.1.0-backend/conf/dao/data_source.properties

```conf
# base spring data source configuration
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/escheduler?characterEncoding=UTF-8
spring.datasource.username=escheduler
spring.datasource.password=escheduler

# connection configuration
spring.datasource.initialSize=5
# min connection number
spring.datasource.minIdle=5
# max connection number
spring.datasource.maxActive=50

# max wait time for get a connection in milliseconds. if configuring maxWait, fair locks are enabled by default and concurrency efficiency decreases.
# If necessary, unfair locks can be used by configuring the useUnfairLock attribute to true.
spring.datasource.maxWait=60000

# milliseconds for check to close free connections
spring.datasource.timeBetweenEvictionRunsMillis=60000

# the Destroy thread detects the connection interval and closes the physical connection in milliseconds if the connection idle time is greater than or equal to minEvictableIdleTimeMillis.
spring.datasource.timeBetweenConnectErrorMillis=60000

# the longest time a connection remains idle without being evicted, in milliseconds
spring.datasource.minEvictableIdleTimeMillis=300000

#the SQL used to check whether the connection is valid requires a query statement. If validation Query is null, testOnBorrow, testOnReturn, and testWhileIdle will not work.
spring.datasource.validationQuery=SELECT 1
#check whether the connection is valid for timeout, in seconds
spring.datasource.validationQueryTimeout=3

# when applying for a connection, if it is detected that the connection is idle longer than time Between Eviction Runs Millis,
# validation Query is performed to check whether the connection is valid
spring.datasource.testWhileIdle=true

#execute validation to check if the connection is valid when applying for a connection
spring.datasource.testOnBorrow=true
#execute validation to check if the connection is valid when the connection is returned
spring.datasource.testOnReturn=false
spring.datasource.defaultAutoCommit=true
spring.datasource.keepAlive=true

# open PSCache, specify count PSCache for every connection
spring.datasource.poolPreparedStatements=true
spring.datasource.maxPoolPreparedStatementPerConnectionSize=20

# data quality analysis is not currently in use. please ignore the following configuration
# task record flag
task.record.flag=false
task.record.datasource.url=jdbc:mysql://localhost:3306/etl?characterEncoding=UTF-8
task.record.datasource.username=escheduler
task.record.datasource.password=escheduler
```

## 执行创建表和导入基础数据脚本

> chmod +x /app/escheduler-1.1.0-backend/script/create_escheduler.sh
> sh /app/escheduler-1.1.0-backend/script/create_escheduler.sh

## 修改免root账户操作

### scp_hosts.sh

vim /app/escheduler-1.1.0-backend/script/scp_hosts.sh

```shell
#!/bin/sh

workDir=`dirname $0`
workDir=`cd ${workDir};pwd`
source $workDir/../conf/config/run_config.conf
source $workDir/../conf/config/install_config.conf

hostsArr=(${ips//,/ })
for host in ${hostsArr[@]}
do

    if ! ssh $host test -e $installPath; then
      ssh $host "mkdir -p $installPath; chown -R $deployUser:$deployUser $installPath"
    fi

        ssh $host  "cd $installPath/; rm -rf bin/ conf/ lib/ script/ sql/"
        scp -r $workDir/../bin  $host:$installPath
        scp -r $workDir/../conf  $host:$installPath
        scp -r $workDir/../lib   $host:$installPath
        scp -r $workDir/../script  $host:$installPath
        scp -r $workDir/../sql  $host:$installPath
        scp  $workDir/../install.sh  $host:$installPath
done
```

### 修改install.sh

vim /app/escheduler-1.1.0-backend/install.sh

```shell
#!/bin/sh

workDir=`dirname $0`
workDir=`cd ${workDir};pwd`

#To be compatible with MacOS and Linux
txt=""
if [[ "$OSTYPE" == "darwin"* ]]; then
    # Mac OSX
    txt="''"
elif [[ "$OSTYPE" == "linux-gnu" ]]; then
    # linux
    txt=""
elif [[ "$OSTYPE" == "cygwin" ]]; then
    # POSIX compatibility layer and Linux environment emulation for Windows
    echo "Easy Scheduler not support Windows operating system"
    exit 1
elif [[ "$OSTYPE" == "msys" ]]; then
    # Lightweight shell and GNU utilities compiled for Windows (part of MinGW)
    echo "Easy Scheduler not support Windows operating system"
    exit 1
elif [[ "$OSTYPE" == "win32" ]]; then
    echo "Easy Scheduler not support Windows operating system"
    exit 1
elif [[ "$OSTYPE" == "freebsd"* ]]; then
    # ...
    txt=""
else
    # Unknown.
    echo "Operating system unknown, please tell us(submit issue) for better service"
    exit 1
fi

source ${workDir}/conf/config/run_config.conf
source ${workDir}/conf/config/install_config.conf

# mysql配置
# mysql 地址,端口
mysqlHost="localhost:3306"

# mysql 数据库名称
mysqlDb="escheduler"

# mysql 用户名
mysqlUserName="escheduler"

# mysql 密码
# 注意：如果有特殊字符，请用 \ 转移符进行转移
mysqlPassword="escheduler"

# conf/config/install_config.conf配置
# 注意：安装路径,不要当前路径(pwd)一样
installPath="/app/escheduler"

# 部署用户
# 注意：部署用户需要有sudo权限及操作hdfs的权限，如果开启hdfs，根目录需要自行创建
deployUser="qingyan"

# zk集群
zkQuorum="localhost:2181"

# 安装hosts
# 注意：安装调度的机器hostname列表，如果是伪分布式，则只需写一个伪分布式hostname即可
ips="localhost"

# conf/config/run_config.conf配置
# 运行Master的机器
# 注意：部署master的机器hostname列表
masters="localhost"

# 运行Worker的机器
# 注意：部署worker的机器hostname列表
workers="localhost"

# 运行Alert的机器
# 注意：部署alert server的机器hostname列表
alertServer="localhost"

# 运行Api的机器
# 注意：部署api server的机器hostname列表
apiServers="localhost"

# alert配置
# 邮件协议
mailProtocol="SMTP"

# 邮件服务host
mailServerHost="smtp.exmail.qq.com"

# 邮件服务端口
mailServerPort="25"

# 发送人
mailSender="xxxxxxxxxx"

# 发送人密码
mailPassword="xxxxxxxxxx"

# TLS邮件协议支持
starttlsEnable="false"

# SSL邮件协议支持
# 注意：默认开启的是SSL协议，TLS和SSL只能有一个处于true状态
sslEnable="true"

# 下载Excel路径
xlsFilePath="/tmp/escheduler/xls"

# 企业微信企业ID配置
enterpriseWechatCorpId="xxxxxxxxxx"

# 企业微信应用Secret配置
enterpriseWechatSecret="xxxxxxxxxx"

# 企业微信应用AgentId配置
enterpriseWechatAgentId="xxxxxxxxxx"

# 企业微信用户配置,多个用户以,分割
enterpriseWechatUsers="xxxxx,xxxxx"


#是否启动监控自启动脚本
monitorServerState="false"

# 资源中心上传选择存储方式：HDFS,S3,NONE
resUploadStartupType="NONE"

# 如果resUploadStartupType为HDFS，defaultFS写namenode地址，支持HA,需要将core-site.xml和hdfs-site.xml放到conf目录下
# 如果是S3，则写S3地址，比如说：s3a://escheduler，注意，一定要创建根目录/escheduler
defaultFS="hdfs://localhost:8020"

# 如果配置了S3，则需要有以下配置
s3Endpoint="http://192.168.199.91:9010"
s3AccessKey="A3DXS30FO22544RE"
s3SecretKey="OloCLq3n+8+sdPHUhJ21XrSxTC+JK"

# resourcemanager HA配置，如果是单resourcemanager,这里为yarnHaIps=""
yarnHaIps="localhost"

# 如果是单 resourcemanager,只需要配置一个主机名称,如果是resourcemanager HA,则默认配置就好
singleYarnIp="localhost"

# hdfs根路径，根路径的owner必须是部署用户。1.1.0之前版本不会自动创建hdfs根目录，需要自行创建
hdfsPath="/escheduler"

# 拥有在hdfs根路径/下创建目录权限的用户
# 注意：如果开启了kerberos，则直接hdfsRootUser=""，就可以
hdfsRootUser="hdfs"

# common 配置
# 程序路径
programPath="/tmp/escheduler"

#下载路径
downloadPath="/tmp/escheduler/download"

# 任务执行路径
execPath="/tmp/escheduler/exec"

# SHELL环境变量路径
shellEnvPath="$installPath/conf/env/.escheduler_env.sh"

# 资源文件的后缀
resSuffixs="txt,log,sh,conf,cfg,py,java,sql,hql,xml"

# 开发状态,如果是true,对于SHELL脚本可以在execPath目录下查看封装后的SHELL脚本,如果是false则执行完成直接删除
devState="true"

# kerberos 配置
# kerberos 是否启动
kerberosStartUp="false"

# kdc krb5 配置文件路径
krb5ConfPath="$installPath/conf/krb5.conf"

# keytab 用户名
keytabUserName="hdfs-mycluster@ESZ.COM"

# 用户 keytab路径
keytabPath="$installPath/conf/hdfs.headless.keytab"

# zk 配置
# zk根目录
zkRoot="/escheduler"

# 用来记录挂掉机器的zk目录
zkDeadServers="/escheduler/dead-servers"

# masters目录
zkMasters="/escheduler/masters"

# workers目录
zkWorkers="/escheduler/workers"

# zk master分布式锁
mastersLock="/escheduler/lock/masters"

# zk worker分布式锁
workersLock="/escheduler/lock/workers"

# zk master容错分布式锁
mastersFailover="/escheduler/lock/failover/masters"

# zk worker容错分布式锁
workersFailover="/escheduler/lock/failover/workers"

# zk master启动容错分布式锁
mastersStartupFailover="/escheduler/lock/failover/startup-masters"

# zk session 超时
zkSessionTimeout="300"

# zk 连接超时
zkConnectionTimeout="300"

# zk 重试间隔
zkRetrySleep="100"

# zk重试最大次数
zkRetryMaxtime="5"


# master 配置
# master执行线程最大数,流程实例的最大并行度
masterExecThreads="100"

# master任务执行线程最大数,每一个流程实例的最大并行度
masterExecTaskNum="20"

# master心跳间隔
masterHeartbeatInterval="10"

# master任务提交重试次数
masterTaskCommitRetryTimes="5"

# master任务提交重试时间间隔
masterTaskCommitInterval="100"

# master最大cpu平均负载,用来判断master是否还有执行能力
masterMaxCpuLoadAvg="10"

# master预留内存,用来判断master是否还有执行能力
masterReservedMemory="1"


# worker 配置
# worker执行线程
workerExecThreads="100"

# worker心跳间隔
workerHeartbeatInterval="10"

# worker一次抓取任务数
workerFetchTaskNum="3"

# worker最大cpu平均负载,用来判断worker是否还有执行能力,保持系统默认，默认为cpu核数的2倍，当负载达到2倍时，
#workerMaxCupLoadAvg="10"

# worker预留内存,用来判断master是否还有执行能力
workerReservedMemory="1"

# api 配置
# api 服务端口
apiServerPort="12345"

# api session 超时
apiServerSessionTimeout="7200"

# api 上下文路径
apiServerContextPath="/escheduler/"

# spring 最大文件大小
springMaxFileSize="1024MB"

# spring 最大请求文件大小
springMaxRequestSize="1024MB"

# api 最大post请求大小
apiMaxHttpPostSize="5000000"

# 1,替换文件
echo "1,替换文件"
sed -i ${txt} "s#spring.datasource.url.*#spring.datasource.url=jdbc:mysql://${mysqlHost}/${mysqlDb}?characterEncoding=UTF-8#g" conf/dao/data_source.properties
sed -i ${txt} "s#spring.datasource.username.*#spring.datasource.username=${mysqlUserName}#g" conf/dao/data_source.properties
sed -i ${txt} "s#spring.datasource.password.*#spring.datasource.password=${mysqlPassword}#g" conf/dao/data_source.properties

sed -i ${txt} "s#org.quartz.dataSource.myDs.URL.*#org.quartz.dataSource.myDs.URL=jdbc:mysql://${mysqlHost}/${mysqlDb}?characterEncoding=UTF-8#g" conf/quartz.properties
sed -i ${txt} "s#org.quartz.dataSource.myDs.user.*#org.quartz.dataSource.myDs.user=${mysqlUserName}#g" conf/quartz.properties
sed -i ${txt} "s#org.quartz.dataSource.myDs.password.*#org.quartz.dataSource.myDs.password=${mysqlPassword}#g" conf/quartz.properties


sed -i ${txt} "s#fs.defaultFS.*#fs.defaultFS=${defaultFS}#g" conf/common/hadoop/hadoop.properties
sed -i ${txt} "s#fs.s3a.endpoint.*#fs.s3a.endpoint=${s3Endpoint}#g" conf/common/hadoop/hadoop.properties
sed -i ${txt} "s#fs.s3a.access.key.*#fs.s3a.access.key=${s3AccessKey}#g" conf/common/hadoop/hadoop.properties
sed -i ${txt} "s#fs.s3a.secret.key.*#fs.s3a.secret.key=${s3SecretKey}#g" conf/common/hadoop/hadoop.properties
sed -i ${txt} "s#yarn.resourcemanager.ha.rm.ids.*#yarn.resourcemanager.ha.rm.ids=${yarnHaIps}#g" conf/common/hadoop/hadoop.properties
sed -i ${txt} "s#yarn.application.status.address.*#yarn.application.status.address=http://${singleYarnIp}:8088/ws/v1/cluster/apps/%s#g" conf/common/hadoop/hadoop.properties


sed -i ${txt} "s#data.basedir.path.*#data.basedir.path=${programPath}#g" conf/common/common.properties
sed -i ${txt} "s#data.download.basedir.path.*#data.download.basedir.path=${downloadPath}#g" conf/common/common.properties
sed -i ${txt} "s#process.exec.basepath.*#process.exec.basepath=${execPath}#g" conf/common/common.properties
sed -i ${txt} "s#hdfs.root.user.*#hdfs.root.user=${hdfsRootUser}#g" conf/common/common.properties
sed -i ${txt} "s#data.store2hdfs.basepath.*#data.store2hdfs.basepath=${hdfsPath}#g" conf/common/common.properties
sed -i ${txt} "s#res.upload.startup.type.*#res.upload.startup.type=${resUploadStartupType}#g" conf/common/common.properties
sed -i ${txt} "s#escheduler.env.path.*#escheduler.env.path=${shellEnvPath}#g" conf/common/common.properties
sed -i ${txt} "s#resource.view.suffixs.*#resource.view.suffixs=${resSuffixs}#g" conf/common/common.properties
sed -i ${txt} "s#development.state.*#development.state=${devState}#g" conf/common/common.properties
sed -i ${txt} "s#hadoop.security.authentication.startup.state.*#hadoop.security.authentication.startup.state=${kerberosStartUp}#g" conf/common/common.properties
sed -i ${txt} "s#java.security.krb5.conf.path.*#java.security.krb5.conf.path=${krb5ConfPath}#g" conf/common/common.properties
sed -i ${txt} "s#login.user.keytab.username.*#login.user.keytab.username=${keytabUserName}#g" conf/common/common.properties
sed -i ${txt} "s#login.user.keytab.path.*#login.user.keytab.path=${keytabPath}#g" conf/common/common.properties

sed -i ${txt} "s#zookeeper.quorum.*#zookeeper.quorum=${zkQuorum}#g" conf/zookeeper.properties
sed -i ${txt} "s#zookeeper.escheduler.root.*#zookeeper.escheduler.root=${zkRoot}#g" conf/zookeeper.properties
sed -i ${txt} "s#zookeeper.escheduler.dead.servers.*#zookeeper.escheduler.dead.servers=${zkDeadServers}#g" conf/zookeeper.properties
sed -i ${txt} "s#zookeeper.escheduler.masters.*#zookeeper.escheduler.masters=${zkMasters}#g" conf/zookeeper.properties
sed -i ${txt} "s#zookeeper.escheduler.workers.*#zookeeper.escheduler.workers=${zkWorkers}#g" conf/zookeeper.properties
sed -i ${txt} "s#zookeeper.escheduler.lock.masters.*#zookeeper.escheduler.lock.masters=${mastersLock}#g" conf/zookeeper.properties
sed -i ${txt} "s#zookeeper.escheduler.lock.workers.*#zookeeper.escheduler.lock.workers=${workersLock}#g" conf/zookeeper.properties
sed -i ${txt} "s#zookeeper.escheduler.lock.failover.masters.*#zookeeper.escheduler.lock.failover.masters=${mastersFailover}#g" conf/zookeeper.properties
sed -i ${txt} "s#zookeeper.escheduler.lock.failover.workers.*#zookeeper.escheduler.lock.failover.workers=${workersFailover}#g" conf/zookeeper.properties
sed -i ${txt} "s#zookeeper.escheduler.lock.failover.startup.masters.*#zookeeper.escheduler.lock.failover.startup.masters=${mastersStartupFailover}#g" conf/zookeeper.properties
sed -i ${txt} "s#zookeeper.session.timeout.*#zookeeper.session.timeout=${zkSessionTimeout}#g" conf/zookeeper.properties
sed -i ${txt} "s#zookeeper.connection.timeout.*#zookeeper.connection.timeout=${zkConnectionTimeout}#g" conf/zookeeper.properties
sed -i ${txt} "s#zookeeper.retry.sleep.*#zookeeper.retry.sleep=${zkRetrySleep}#g" conf/zookeeper.properties
sed -i ${txt} "s#zookeeper.retry.maxtime.*#zookeeper.retry.maxtime=${zkRetryMaxtime}#g" conf/zookeeper.properties

sed -i ${txt} "s#master.exec.threads.*#master.exec.threads=${masterExecThreads}#g" conf/master.properties
sed -i ${txt} "s#master.exec.task.number.*#master.exec.task.number=${masterExecTaskNum}#g" conf/master.properties
sed -i ${txt} "s#master.heartbeat.interval.*#master.heartbeat.interval=${masterHeartbeatInterval}#g" conf/master.properties
sed -i ${txt} "s#master.task.commit.retryTimes.*#master.task.commit.retryTimes=${masterTaskCommitRetryTimes}#g" conf/master.properties
sed -i ${txt} "s#master.task.commit.interval.*#master.task.commit.interval=${masterTaskCommitInterval}#g" conf/master.properties
#sed -i ${txt} "s#master.max.cpuload.avg.*#master.max.cpuload.avg=${masterMaxCpuLoadAvg}#g" conf/master.properties
sed -i ${txt} "s#master.reserved.memory.*#master.reserved.memory=${masterReservedMemory}#g" conf/master.properties


sed -i ${txt} "s#worker.exec.threads.*#worker.exec.threads=${workerExecThreads}#g" conf/worker.properties
sed -i ${txt} "s#worker.heartbeat.interval.*#worker.heartbeat.interval=${workerHeartbeatInterval}#g" conf/worker.properties
sed -i ${txt} "s#worker.fetch.task.num.*#worker.fetch.task.num=${workerFetchTaskNum}#g" conf/worker.properties
#sed -i ${txt} "s#worker.max.cpuload.avg.*#worker.max.cpuload.avg=${workerMaxCupLoadAvg}#g" conf/worker.properties
sed -i ${txt} "s#worker.reserved.memory.*#worker.reserved.memory=${workerReservedMemory}#g" conf/worker.properties


sed -i ${txt} "s#server.port.*#server.port=${apiServerPort}#g" conf/application.properties
sed -i ${txt} "s#server.session.timeout.*#server.session.timeout=${apiServerSessionTimeout}#g" conf/application.properties
sed -i ${txt} "s#server.context-path.*#server.context-path=${apiServerContextPath}#g" conf/application.properties
sed -i ${txt} "s#spring.http.multipart.max-file-size.*#spring.http.multipart.max-file-size=${springMaxFileSize}#g" conf/application.properties
sed -i ${txt} "s#spring.http.multipart.max-request-size.*#spring.http.multipart.max-request-size=${springMaxRequestSize}#g" conf/application.properties
sed -i ${txt} "s#server.max-http-post-size.*#server.max-http-post-size=${apiMaxHttpPostSize}#g" conf/application.properties


sed -i ${txt} "s#mail.protocol.*#mail.protocol=${mailProtocol}#g" conf/alert.properties
sed -i ${txt} "s#mail.server.host.*#mail.server.host=${mailServerHost}#g" conf/alert.properties
sed -i ${txt} "s#mail.server.port.*#mail.server.port=${mailServerPort}#g" conf/alert.properties
sed -i ${txt} "s#mail.sender.*#mail.sender=${mailSender}#g" conf/alert.properties
sed -i ${txt} "s#mail.passwd.*#mail.passwd=${mailPassword}#g" conf/alert.properties
sed -i ${txt} "s#mail.smtp.starttls.enable.*#mail.smtp.starttls.enable=${starttlsEnable}#g" conf/alert.properties
sed -i ${txt} "s#mail.smtp.ssl.enable.*#mail.smtp.ssl.enable=${sslEnable}#g" conf/alert.properties
sed -i ${txt} "s#xls.file.path.*#xls.file.path=${xlsFilePath}#g" conf/alert.properties
sed -i ${txt} "s#enterprise.wechat.corp.id.*#enterprise.wechat.corp.id=${enterpriseWechatCorpId}#g" conf/alert.properties
sed -i ${txt} "s#enterprise.wechat.secret.*#enterprise.wechat.secret=${enterpriseWechatSecret}#g" conf/alert.properties
sed -i ${txt} "s#enterprise.wechat.agent.id.*#enterprise.wechat.agent.id=${enterpriseWechatAgentId}#g" conf/alert.properties
sed -i ${txt} "s#enterprise.wechat.users.*#enterprise.wechat.users=${enterpriseWechatUsers}#g" conf/alert.properties


sed -i ${txt} "s#installPath.*#installPath=${installPath}#g" conf/config/install_config.conf
sed -i ${txt} "s#deployUser.*#deployUser=${deployUser}#g" conf/config/install_config.conf
sed -i ${txt} "s#ips.*#ips=${ips}#g" conf/config/install_config.conf


sed -i ${txt} "s#masters.*#masters=${masters}#g" conf/config/run_config.conf
sed -i ${txt} "s#workers.*#workers=${workers}#g" conf/config/run_config.conf
sed -i ${txt} "s#alertServer.*#alertServer=${alertServer}#g" conf/config/run_config.conf
sed -i ${txt} "s#apiServers.*#apiServers=${apiServers}#g" conf/config/run_config.conf


# 2,创建目录
echo "2,创建目录"

if [ ! -d $installPath ];then
  mkdir -p $installPath
  chown -R $deployUser:$deployUser $installPath
fi

hostsArr=(${ips//,/ })
for host in ${hostsArr[@]}
do

# 如果programPath不存在,则创建
if ! ssh $host test -e $programPath; then
  ssh $host "mkdir -p $programPath;chown -R $deployUser:$deployUser $programPath"
fi

# 如果downloadPath不存在,则创建
if ! ssh $host test -e $downloadPath; then
  ssh $host "mkdir -p $downloadPath;chown -R $deployUser:$deployUser $downloadPath"
fi

# 如果$execPath不存在,则创建
if ! ssh $host test -e $execPath; then
  ssh $host "mkdir -p $execPath; chown -R $deployUser:$deployUser $execPath"
fi

# 如果$xlsFilePath不存在,则创建
if ! ssh $host test -e $xlsFilePath; then
  ssh $host "mkdir -p $xlsFilePath; chown -R $deployUser:$deployUser $xlsFilePath"
fi

done


# 3,停止服务
echo "3,停止服务"
sh ${workDir}/script/stop_all.sh

# 4,删除zk节点
echo "4,删除zk节点"
sleep 1
python ${workDir}/script/del_zk_node.py $zkQuorum $zkRoot

# 5,scp资源
echo "5,scp资源"
sh ${workDir}/script/scp_hosts.sh
if [ $? -eq 0 ]
then
	echo 'scp拷贝完成'
else
	echo 'sc 拷贝失败退出'
	exit -1
fi

# 6,启动
echo "6,启动"
sh ${workDir}/script/start_all.sh

# 7,启动监控自启动脚本
monitor_pid=${workDir}/monitor_server.pid
if [ "true" = $monitorServerState ];then
        if [ -f $monitor_pid ]; then
                TARGET_PID=`cat $monitor_pid`
                if kill -0 $TARGET_PID > /dev/null 2>&1; then
                        echo "monitor server running as process ${TARGET_PID}.Stopping"
                        kill $TARGET_PID
                        sleep 5
                        if kill -0 $TARGET_PID > /dev/null 2>&1; then
                                echo "monitor server did not stop gracefully after 5 seconds: killing with kill -9"
                                kill -9 $TARGET_PID
                        fi
                else
                        echo "no monitor server to stop"
                fi
                echo "monitor server running as process ${TARGET_PID}.Stopped success"
                rm -f $monitor_pid
        fi
        nohup python -u ${workDir}/script/monitor_server.py $installPath $zkQuorum $zkMasters $zkWorkers > ${workDir}/monitor_server.log 2>&1 &
        echo $! > $monitor_pid
        echo "start monitor server success as process `cat $monitor_pid`"

fi

```

### 修改escheduler-daemon.sh

vim /app/escheduler-1.1.0-backend/bin/escheduler-daemon.sh

```shell
export JAVA_HOME=
```

## 安装

> chmod +x /app/escheduler-1.1.0-backend/install.sh
> cd /app/escheduler-1.1.0-backend
> ./install.sh
