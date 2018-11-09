# HL-2018-setup-ClickHouse

```sh
sudo lxc launch ubuntu:16.04 zookeeper-dev
sudo lxc config device add ts-clickhouse eth1 nic name=eth1 nictype=bridged parent=lxdbr0


sudo lxc launch ubuntu:16.04 ch1-r1-dev
sudo lxc launch ubuntu:16.04 ch1-r2-dev
sudo lxc launch ubuntu:16.04 ch1-r3-dev


sudo lxc launch ubuntu:16.04 ch2-r1-dev
sudo lxc launch ubuntu:16.04 ch2-r2-dev
sudo lxc launch ubuntu:16.04 ch2-r3-dev


lxc config set cas-n1-dev limits.cpu 4
lxc config set cas-n1-dev limits.memory 2GB

lxc config set cas-n2-dev limits.cpu 4
lxc config set cas-n2-dev limits.memory 2GB

lxc config set cas-zookeeper-dev limits.cpu 1
lxc config set cas-zookeeper-dev limits.memory 1GB

```


```
kshvakov@staging:~$ sudo lxc list
|       NAME       |  STATE  |        IPV4         | IPV6 |    TYPE    | SNAPSHOTS |
+------------------+---------+---------------------+------+------------+-----------+
| ch1-r1-dev       | RUNNING | 10.70.68.176 (eth0) |      | PERSISTENT | 0         |
+------------------+---------+---------------------+------+------------+-----------+
| ch1-r2-dev       | RUNNING | 10.70.68.213 (eth0) |      | PERSISTENT | 0         |
+------------------+---------+---------------------+------+------------+-----------+
| ch1-r3-dev       | RUNNING | 10.70.68.182 (eth0) |      | PERSISTENT | 0         |
+------------------+---------+---------------------+------+------------+-----------+
| ch2-r1-dev       | RUNNING | 10.70.68.140 (eth0) |      | PERSISTENT | 0         |
+------------------+---------+---------------------+------+------------+-----------+
| ch2-r2-dev       | RUNNING | 10.70.68.49 (eth0)  |      | PERSISTENT | 0         |
+------------------+---------+---------------------+------+------------+-----------+
| ch2-r3-dev       | RUNNING | 10.70.68.122 (eth0) |      | PERSISTENT | 0         |
+------------------+---------+---------------------+------+------------+-----------+
| kafka-node-1-dev | RUNNING | 10.70.68.53 (eth0)  |      | PERSISTENT | 0         |
+------------------+---------+---------------------+------+------------+-----------+
| zookeeper-dev    | RUNNING | 10.70.68.202 (eth0) |      | PERSISTENT | 0         |
+------------------+---------+---------------------+------+------------+-----------+
```



sudo apt-key adv --keyserver keyserver.ubuntu.com --recv E0C56BD4    # optional

echo "deb http://repo.yandex.ru/clickhouse/deb/stable/ main/" | sudo tee /etc/apt/sources.list.d/clickhouse.list
sudo apt-get update

# sudo apt-get install -y clickhouse-server clickhouse-client

sudo apt-get install -y clickhouse-client=1.1.54343 \
    clickhouse-server-base=1.1.54343 \
    clickhouse-server-common=1.1.54343


sudo service clickhouse-server start

clickhouse-client




```xml
<interserver_http_host>10.70.68.176</interserver_http_host>
```

```xml
<zookeeper>
    <node index="1">
        <host>10.70.68.202</host>
        <port>2181</port>
    </node>
</zookeeper>
```

```xml
<macros>
    <shard>02</shard>
    <replica>ch2-r3-dev</replica>
</macros>
```

```xml
<api>
    <password></password>
    <networks incl="networks" replace="replace">
        <ip>::/0</ip>
    </networks>
    <profile>default</profile>
    <quota>default</quota>
</api>
<worker>
    <password></password>
    <networks incl="networks" replace="replace">
        <ip>::/0</ip>
    </networks>
    <profile>default</profile>
    <quota>default</quota>
</worker>
```

```xml
<remote_servers incl="clickhouse_remote_servers" >
    <trafficstars>
        <shard>
            <replica>
                <host>10.70.68.176</host>
                <port>9000</port>
            </replica>
            <replica>
                <host>10.70.68.213</host>
                <port>9000</port>
            </replica>
            <replica>
                <host>10.70.68.182</host>
                <port>9000</port>
            </replica>
        </shard>
        <shard>
            <replica>
                <host>10.70.68.140</host>
                <port>9000</port>
            </replica>
            <replica>
                <host>10.70.68.49</host>
                <port>9000</port>
            </replica>
            <replica>
                <host>10.70.68.122</host>
                <port>9000</port>
            </replica>
        </shard>
    </trafficstars>
</remote_servers>
```


## Kafka 2.0

```
sudo lxc launch ubuntu:16.04 kafka-node-1-dev
sudo lxc config device add kafka-node-1-dev eth1 nic name=eth1 nictype=bridged parent=lxdbr0

sudo apt-get update -y
sudo apt-get upgrade -y
sudo add-apt-repository -y ppa:webupd8team/java
sudo apt-get update -y
sudo apt-get install oracle-java8-installer -y
sudo java -version
sudo apt-get install zookeeperd -y
netstat -ant | grep :2181

```



```
 cat > /etc/systemd/system/kafka.service


[Unit]
Description=Kafka Daemon
After=network.target

[Service]
Type=simple
User=root
Group=root

ExecStart=/opt/kafka/bin/kafka-server-start.sh /opt/kafka/config/server.properties

StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=kafka

[Install]
WantedBy=multi-user.target


chmod 664 /etc/systemd/system/kafka.service

```
systemctl daemon-reload
systemctl start kafka.service
systemctl enable kafka.service