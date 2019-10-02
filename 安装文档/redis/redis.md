redis 

info 查看redis信息

```shell
#下载
wget http://download.redis.io/releases/redis-5.0.5.tar.gz
tar -zxvf redis-5.0.5.tar.gz
cd redis-5.0.5
make & make install

#启动 单节点启动完成
src/redis-server redis.conf


# 创建目录
mkdir 7001 7002 7003 7004 7005 7006
cp redis.conf 7001
----------------
cp redis.conf 7006
# 配置文件修改
daemonize yes （后台启动）

port 8001（分别对每个机器的端口号进行设置）

dir /usr/local/redis-cluster/8001/（指定数据文件存放位置，必须要指定不同的目录位置，不然会丢失数据）

cluster-enabled yes（启动集群模式）

cluster-config-file nodes-8001.conf（集群节点信息文件，这里800x最好和port对应上）

cluster-node-timeout 5000

bind 127.0.0.1（去掉bind绑定访问ip信息）

protected-mode no （关闭保护模式）

appendonly yes （持久化）

如果要设置密码需要增加如下配置：

requirepass xxx (设置redis访问密码)

masterauth xxx (设置集群节点间访问密码，跟上面一致)

vim批量替换：
%s/源字符串/目的字符串/g
```

![1569991105129](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1569991105129.png)

创建集群：

```shell
#创建集群，--cluster-replicas 1指定从库数量1
cd /usr/local/redis-5.0.5/src
redis-cli --cluster create 192.168.31.111:9000 192.168.31.112:9000 192.168.31.113:9000 192.168.31.111:9001 192.168.31.112:9001 192.168.31.113:9001 --cluster-replicas 1

#创建顺序三个主，三个从，前面三个是主后面三个是从

#配置密码启动
redis-cli -a xxx --cluster create 192.168.31.111:9000 192.168.31.112:9000 192.168.31.113:9000 192.168.31.111:9001 192.168.31.112:9001 192.168.31.113:9001 --cluster-replicas 1
```

![1569991703191](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1569991703191.png)

![1569991745577](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\1569991745577.png)

