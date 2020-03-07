# shell使用pid与ps kill程序

## ps法

```shell
#!/bin/bash

FLUME_HOME="/data/server/flume-1.9.0"
FLUME_CONF_FILE="flume-conf.properties"
FLUME_NAME="flume"
FLUME_AGENT="zdAgent"

num=`ps -ef|grep java|grep $FLUME_NAME|wc -l`

function start() {
    echo "begin start flume process ...."
    if [ "$num" = "0" ]; then
        nohup flume-ng agent --conf $FLUME_HOME/conf --conf-file $FLUME_HOME/conf/flume-conf.properties --name zdAgent &
        echo "start success..."
        echo "log: $FLUME_HOME/logs/flume.log"
    else
        echo "flume process exists, start failed!"
    exit 0
    fi
}

function stop() {
    echo "begin stop flume process..."
    if [ "$num" = "0" ]; then
         echo "flume process stoped."
    else
         ps -ef|grep java|grep $FLUME_NAME|awk '{print $2;}'|xargs kill -9
         echo "flume process stop success!"
    fi
}

function restart() {
    stop
    sleep 5
    start
}

case "$1" in
    "start")
        start
        ;;
     "stop")
        stop
    ;;
     "restart")
        restart
        ;;
     *)
        echo "please enter start, stop, restart"
esac
```

## pid法

```shell
#!/bin/bash

FLUME_HOME="/data/server/flume-1.9.0"
FLUME_CONF_FILE="flume-conf.properties"
FLUME_NAME="flume"
FLUME_AGENT="zdAgent"

function start() {
    echo "begin start flume process ...."
    if [ ! -f "$FLUME_HOME/pid" ]; then
        nohup flume-ng agent --conf $FLUME_HOME/conf --conf-file $FLUME_HOME/conf/flume-conf.properties --name zdAgent & echo $! > $FLUME_HOME/pid
        echo "start success..."
        echo "log: $FLUME_HOME/logs/flume.log"
    else
        echo "flume process exists, start failed!"
    fi
    exit 0
}

function stop() {
    if [ ! -f "$FLUME_HOME/pid" ]; then
        echo "flume process stoped."
    else
        kill -9 `cat $FLUME_HOME/pid`
        rm $FLUME_HOME/pid
        echo "flume process stop success!"
    fi
}

function restart() {
    stop
    sleep 3
    start
}

case "$1" in
    "start")
        start
        ;;
     "stop")
        stop
    ;;
     "restart")
        restart
        ;;
     *)
        echo "please enter start, stop, restart"
esac
```
