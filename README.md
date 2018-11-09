```sh
sudo lxc launch ubuntu:16.04 zookeeper-hl-2018



sudo lxc launch ubuntu:16.04 ch1-hl-2018
sudo lxc launch ubuntu:16.04 ch2-hl-2018



lxc config set ch1-hl-2018 limits.cpu 4
lxc config set ch1-hl-2018 limits.memory 3GB

lxc config set ch2-hl-2018 limits.cpu 4
lxc config set ch2-hl-2018 limits.memory 3GB

lxc config set zookeeper-hl-2018 limits.cpu 1
lxc config set zookeeper-hl-2018 limits.memory 1GB


sudo lxc config device add ch1-hl-2018  eth1 nic name=eth1 nictype=bridged parent=lxdbr0
sudo lxc config device add ch2-hl-2018  eth1 nic name=eth1 nictype=bridged parent=lxdbr0
sudo lxc config device add zookeeper-hl-2018  eth1 nic name=eth1 nictype=bridged parent=lxdbr0

```

lxc shell zookeeper-hl-2018


```
sudo apt-get update -y
sudo apt-get upgrade -y
sudo add-apt-repository -y ppa:webupd8team/java
sudo apt-get update -y
sudo apt-get install oracle-java8-installer -y
sudo java -version
sudo apt-get install zookeeperd -y
netstat -ant | grep :2181
```

lxc shell ch1-hl-2018

```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv E0C56BD4    # optional

echo "deb http://repo.yandex.ru/clickhouse/deb/stable/ main/" | sudo tee /etc/apt/sources.list.d/clickhouse.list
sudo apt-get update

sudo apt-get install -y clickhouse-server clickhouse-client


sudo service clickhouse-server start

clickhouse-client
```



```xml
<interserver_http_host>10.112.179.249</interserver_http_host>
```

```xml
<?xml version="1.0"?>
<yandex>
 <zookeeper>
     <node index="1">
         <host>10.112.179.58</host>
         <port>2181</port>
     </node>
 </zookeeper>
</yandex>
```

```xml
<?xml version="1.0"?>
<yandex>
<macros>
    <shard>02</shard>
    <replica>replica1</replica>
</macros>
</yandex>
```


```xml
<?xml version="1.0"?>
<yandex>
<remote_servers incl="clickhouse_remote_servers" >
    <trafficstars>
        <shard>
            <replica>
                <host>10.112.179.218</host>
                <port>9000</port>
            </replica>
        </shard>
        <shard>
            <replica>
                <host>10.112.179.249</host>
                <port>9000</port>
            </replica>
        </shard>
    </trafficstars>
</remote_servers>
</yandex>
```
