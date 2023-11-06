### 一、NoSQL简介

#### 1、NoSQL是什么

- NoSQL=Not Only SQL，意为不仅仅是SQL，泛指```非关系型数据库```
- NoSQL不遵循SQL标准，即不能使用SQL语句操作数据库
- 不支持ACID
- 由于数据都存在内存中，性能非常好

#### 2、NoSQL有哪些

- 列式数据库
  - 数据存储以```列```（column）为单位，例如：Hbase、Cassandra等
  - 特点：快速查询、高扩展性、易于实现分布式扩展
  - 应用场景：在分布式文件系统之上，提供支持随机读写的分布式数据存储
- 文档数据库
  - 每一行数据组织为一个文档，以文档为单位存储数据，例如：MongoDB、ElasticSearch等
  - 特点：数据模型无需事先定义
  - 应用场景：非强实物需求的web应用
- 键值数据库
  - 以键值对的形式存储数据，例如：```Redis```、DynanoDB等
  - 特点：查询数据极快
  - 应用场景：内容缓存、高并发、高负载场景
- 图形数据库
  - 以图形的方式存储数据，例如：Neo4J、Giraph等
  - 特点：用图形表示现实世界的关系很直接、自然、易于建模，且可以很高效的查询关联的数据
  - 应用场景：社交网络、推荐系统、关系图谱等

#### 3、NoSQL有什么用

- 配合关系型数据库做数据缓存、降低数据库IO压力
- 分布式系统中，可以共享session
- 可以做排行榜、手机验证码、计数器、秒杀、去重、构建队列等



### 二、Redis6简介和安装

#### 1、Redis简介

- Redis是一个开源的键值类型的数据库
- Redis支持多种数据类型：string、list、set、hash、zset等
- Redis中的数据都存储在内存中
- Redis会定期把数据持久化到磁盘中

#### 2、安装Redis

- 安装gcc（已安装则跳过）

  ```shell
  # 1、安装gcc
  yum install gcc
  
  # 2、查看gcc版本
  gcc --version
  gcc (GCC) 4.8.5 20150623 (Red Hat 4.8.5-44)
  Copyright © 2015 Free Software Foundation, Inc.
  本程序是自由软件；请参看源代码的版权声明。本软件没有任何担保；
  包括没有适销性和某一专用目的下的适用性担保。
  ```

- 下载Redis安装包（以6.2.1为例）

  ```shell
  [root@localhost opt]# pwd
  /opt
  [root@localhost opt]# ls -l
  -rw-r--r--. 1 root root 2438367 10月 14 00:25 redis-6.2.1.tar.gz
  drwxr-xr-x. 2 root root       6 10月 31 2018 rh
  ```

- 解压安装包

  ```shell
  [root@localhost opt]# tar -zxvf redis-6.2.1.tar.gz
  
  # 解压后查看目录下文件，多了一个redis-6.2.1目录
  [root@localhost opt]# ls -l
  总用量 2388
  drwxrwxr-x. 7 root root    4096 3月   2 2021 redis-6.2.1
  -rw-r--r--. 1 root root 2438367 10月 14 00:25 redis-6.2.1.tar.gz
  drwxr-xr-x. 2 root root       6 10月 31 2018 rh
  
  ```

- 进入解压后的目录，执行```make```命令（编译）

  ```shell
  [root@localhost opt]# cd redis-6.2.1/
  [root@localhost redis-6.2.1]# make
  ```

- ```如果执行make报错```

  ```shell
  # "jemalloc/jemalloc.h：没有那个文件或目录"
  # 解决方法: 在redis-6.2.1目录下执行make distclean命令
  [root@localhost redis-6.2.1]# make distclean
  
  # 完成后，再次执行make命令
  [root@localhost redis-6.2.1]# make
  ```

- 执行make install命令

  ```shell
  [root@localhost redis-6.2.1]# make install
  ```

- 安装后的信息

  ```shell
  # Redis默认安装目录
  /usr/local/bin
  
  # 查看安装目录
  [root@localhost redis-6.2.1]# cd /usr/local/bin/
  [root@localhost bin]# ls -l
  总用量 18848
  -rw-r--r--. 1 root root     305 11月  6 14:21 dump.rdb
  -rwxr-xr-x. 1 root root 4833352 10月 14 00:35 redis-benchmark
  lrwxrwxrwx. 1 root root      12 10月 14 00:35 redis-check-aof -> redis-server
  lrwxrwxrwx. 1 root root      12 10月 14 00:35 redis-check-rdb -> redis-server
  -rwxr-xr-x. 1 root root 5003368 10月 14 00:35 redis-cli
  lrwxrwxrwx. 1 root root      12 10月 14 00:35 redis-sentinel -> redis-server
  -rwxr-xr-x. 1 root root 9450208 10月 14 00:35 redis-server
  [root@localhost bin]# 
  
  # Redis服务端启动：前台启动（不推荐）
  [root@localhost bin]# redis-server
  
  # Redis服务端启动：后台启动（推荐）
  # 1、拷贝一份Redis配置文件到 /etc/目录下
  [root@localhost bin]# cp /opt/redis.conf /etc/
  
  # 2、编辑/ect/redis.conf
  daemonize no 改为 yes （允许后台启动）
  bind 127.0.0.1 改为 0.0.0.0（允许其他主机连接Redis）
  
  # 3、启动Redis服务端
  [root@localhost bin]# redis-server /etc/redis.conf
  
  # 4、查看Redis进程是否已启动
  [root@localhost bin]# ps -ef | grep redis
  root      1231     1  0 14:27 ?        00:00:11 /usr/local/bin/redis-server 0.0.0.0:6379
  root      3031  1744  0 15:39 pts/1    00:00:00 grep --color=auto redis
  
  # 5、连接Redis客户端，并测试连接
  # (1) 单实例，默认6379端口
  [root@localhost bin]# redis-cli 
  127.0.0.1:6379> ping
  PONG
  
  # (2) 多实例，指定端口连接
  [root@localhost bin]# redis-cli -p 6379
  127.0.0.1:6379> ping
  PONG
  
  # 6、关闭Redis服务端
  # (1)方式1
  [root@localhost bin]# redis-cli shutdown
  
  # (2)方式2：连接Redis之后再关闭
  [root@localhost bin]# redis-cli 
  127.0.0.1:6379> shutdown
  not connected> 
  
  # (3)方式3：多实例，指定端口关闭
  [root@localhost bin]# redis-cli -p 6379 shutdown
  ```

#### 3、Redis开机自启

- 新建```redis.service```文件

```shell
# 1、进入/usr/lib/systemd/system目录
[root@localhost bin]# cd /usr/lib/systemd/system

# 2、新建redis.service文件，并填入以下内容
[Unit]
Description=The redis-server Process Manager
After=syslog.target network.target

[Service]
Type=forking
PIDFile=/var/run/redis_6379.pid
# 启动Redis服务端命令, 根据Redis安装目录和配置文件目录填入
ExecStart=/usr/local/bin/redis-server /etc/redis.conf
ExecReload=/bin/kill -USR2 $MAINPID
ExecStop=/bin/kill -SIGINT $MAINPID

[Install]
WantedBy=multi-user.target

# 3、重新加载服务文件
[root@localhost system]# systemctl daemon-reload

# 4、启动Redis服务
[root@localhost system]# systemctl start redis.service

# 5、设置Redis服务开机自启
[root@localhost system]# systemctl enable redis.service
```



### 三、Redis6数据类型

#### 1、String

- String是Redis最基本的数据类型，格式为：一个```key```对应一个```value```，其中```key```固定为字符串类型，String是指```value```的类型

- Redis中String对应的value最大为512M

- 常用命令

  ```shell
  # 1、清空当前数据库
  flushdb
  "OK"
  
  # 2、查看当前数据库的所有key
  keys *
  
  # 3、切换数据库(select后面数字为数据库编号)
  select 1
  "OK"
  
  # 4、设置String类型的数据
  set key1 value1
  "OK"
  
  # 5、获取key对应的value值(不存在则返回null)
  get key1
  "value1"
  
  # 6、在指定key对应的value后面追加字符串, 返回总长度(key不存在也可以追加, 相当于set)
  append key1 2023
  "10"
  get key1
  "value12023"
  
  # 7、获取指定key对应value的长度
  strlen key1
  "10"
  
  # 8、指定key不存在时才添加, 否则不添加(nx表示不存在: not exist)
  # 返回1表示添加成功, 返回0表示添加失败
  setnx key2 value2
  "1"
  get key2
  "value2"
  
  # 9、使指定key对应的value增加1, 返回增加后的值（只有value为数字类型才可以使用）
  set key3 33
  "OK"
  incr key3
  "34"
  
  # 10、使指定key对应的value减少1, 返回减少后的值（只有value为数字类型才可以使用）
  decr key3
  "33"
  
  # 11、使指定key对应的value增加或减少指定数值（只有value为数字类型才可以使用）
  incrby key3 11
  "44"
  
  decrby key3 20
  "24"
  
  # 12、一次添加多个String类型数据
  mset key4 value4 key5 value5
  "OK"
  
  # 13、一次获取多个key对应的value值
  mget key4 key5
   1)  "value4"
   2)  "value5"
   
  # 14、一次添加多个String类型数据（只要有一个key已经存在，则全部添加失败）
  # 返回1表示添加成功, 返回0表示添加失败
  
  # （1）key5已经存在，则key5、key6全部添加失败
  msetnx key5 value55 key6 value6
  "0"
  mget key5 key6
   1)  "value5"
   2)  null
  
  # （2）key6、key7都不存在，所以key6、key7成功
  msetnx key6 value6 key7 value7
  "1"
  mget key6 key7
   1)  "value6"
   2)  "value7"
   
  # 15、获取key对应value的索引范围内的值(索引从0开始，前后都包括)
  get key5
  "value5"
  
  getrange key5 0 1
  "va"
  
  getrange key5 1 3
  "alu"
  
  getrange key5 1 -1
  "alue5"
  
  # 16、添加key的同时，设置key的过期时间，单位：秒
  # 如果key已存在，则会覆盖原来的value
  setex key5 10 v5
  "OK"
  
  # 17、查看key的过期时间（单位：秒）
  ttl key5
  "4"
  ttl key5
  "3"
  ```

  

#### 2、List

- List是简单的字符串列表，表现形式为单键多值：```key value1 value2 value3 ...```

- 常用命令：

  ```shell
  # 1、从列表左边（头部）添加值，返回列表总长度
  lpush k1 v11 v12 v13
  (integer) 3
  
  lrange k1 0 -1
  1) "v13"
  2) "v12"
  3) "v11"
  
  # 2、从列表右边（尾部）添加值，返回列表总长度
  rpush k2 v21 v22 v23
  (integer) 3
  
  lrange k2 0 -1
  1) "v21"
  2) "v22"
  3) "v23"
  
  # 3、 从列表左边（头部）弹出一个value (弹出后, 该value将从列表中移除, 如果所有value都被移除, key也会被移除)
  lpop k1
  "v13"
  
  lrange k1 0 -1
  1) "v12"
  2) "v11"
  
  # 4、 从列表右边（尾部）弹出一个value (弹出后, 该value将从列表中移除, 如果所有value都被移除, key也会被移除)
  rpop k2
  "v23"
  
  lrange k2 0 -1
  1) "v21"
  2) "v22"
  
  # 5、从列表右边（尾部）弹出一个值，添加到指定列表的左边（头部）
  rpoplpush k1 k2
  "v11"
  
  # 6、查看列表中的值(索引从0开始，-1表示最大索引)
  lrange k1 0 -1
  1) "v12"
  
  lrange k2 0 -1
  1) "v11"
  2) "v21"
  3) "v22"
  
  # 7、按照索引获取列表中的元素
  lindex k2 0
  "v11"
  
  lindex k2 1
  "v22"
  
  # 8、获取列表的长度
  llen k2
  (integer) 3
  
  # 9、在列表指定元素之前/之后插入一个元素，返回插入之后列表总长度（-1表示插入失败）
  # (1) 在v11元素之前插入一个v10元素
  linsert k2 before v11 v10
  (integer) 4
  
  lrange k2 0 -1
  1) "v10"
  2) "v11"
  3) "v21"
  4) "v22"
  
  # (2) 在v22元素之后插入一个元素v23
  linsert k2 after v22 v23
  (integer) 5
  
  lrange k2 0 -1
  1) "v10"
  2) "v11"
  3) "v21"
  4) "v22"
  5) "v23"
  
  # 10、从从列表左边开始，移除n个指定value的元素（返回成功移除的元素个数，0表示移除失败）
  # 从列表左边开始, 移除1个v10
  lrem k2 1 v10
  
  lrange k2 0 -1
  1) "v11"
  2) "v21"
  3) "v22"
  4) "v23"
  
  # 11、替换列表中指定索引的value值（如果索引不存在，会报错）
  lset k2 0 v20
  OK
  
  lrange k2 0 -1
  1) "v20"
  2) "v21"
  3) "v22"
  4) "v23"
  ```

#### 3、Set

- Set与List的功能类似，都是一个键对应多个值：```key value1 value2 value3 ...```

- 不同之处：Set中的元素不能重复，且Set提供了接口判断某个元素是否在Set中，这是List没有的

- 常用命令

  ```shell
  # 1、添加元素到Set中, 已经存在的元素将不会重复添加
  sadd k3 v31 v32 v33
  (integer) 3
  
  # 2、查看Set中的所有元素
  smembers k3
  1) "v33"
  2) "v32"
  3) "v31"
  
  # 3、判断Set中是否存在某个元素（返回0表示不存在，返回1表示存在）
  sismember k3 v2
  (integer) 0
  
  sismember k3 v33
  (integer) 1
  
  # 4、查看Set中元素个数
  scard k3
  (integer) 3
  
  # 5、移除Set中的某个元素（返回1表示移除成功，返回0表示移除失败）
  srem k3 v33
  (integer) 1
  
  smembers k3
  1) "v32"
  2) "v31"
  
  # 6、随机从Set中弹出一个元素（如果元素全部被弹出，则Set的key也将被移除）
  spop k3
  "v32"
  spop k3
  "v31"
  
  # 7、随机查看集合中n个元素（不会删除Set中的元素）
  sadd k3 v35 v36 v37 v38
  (integer) 4
  
  srandmember k3 2
  1) "v37"
  2) "v38"
  
  srandmember k3 2
  1) "v36"
  2) "v38"
  
  # 8、从一个Set中移动一个元素，到另一个Set中
  sadd k4 v41 v42 v43
  (integer) 3
  
  smembers k3
  1) "v36"
  2) "v35"
  3) "v38"
  4) "v37"
  
  smembers k4
  1) "v43"
  2) "v41"
  3) "v42"
  
  # 把k3中的v36元素，移动到k4中
  smove k3 k4 v36
  (integer) 1
  
  smembers k3
  1) "v35"
  2) "v38"
  3) "v37"
  
  smembers k4
  1) "v36"
  2) "v43"
  3) "v41"
  4) "v42"
  
  # 9、查看多个Set的交集（同时在多个集合中的元素）
  sinter k3 k4
  (empty array)
  
  # 10、查看多个Set的并集（多个集合的元素合并）
  sunion k3 k4
  1) "v37"
  2) "v38"
  3) "v36"
  4) "v35"
  5) "v42"
  6) "v43"
  7) "v41"
  
  # 11、查看多个Set的差集（在key1中，但是不在key2中的元素）
  sdiff k3 k4
  1) "v35"
  2) "v37"
  3) "v38"
  ```

#### 4、Hash

- Redis中Hash是一个键值对的集合，表现形式为：一个```key```对应多个```field```和```value```

- 这种结构特别适合存储对象，类似于Java中```Map<String, Object>```

- 常用命令

  ```shell
  # 1、添加一个hash数据, 如果字段已存在, 则会覆盖原值
  # (1) 设置单个字段
  hset user id 1001
  (integer) 1
  
  hset user name zhangsan
  (integer) 1
  
  hset user age 18
  (integer) 1
  
  # (2) 一次性设置多个字段
  hset user id 1001 name zhangsan age 18
  (integer) 3
  
  # 2、添加一个hash数据, 仅当字段不存在时, 才会设置其值, 否则不设置
  # (1) user的id字段已存在, 设置失败
  hsetnx user id 1111
  (integer) 0
  
  hvals user
  1) "1001"
  2) "zhangsan"
  3) "18"
  
  # (2) user不存在gender字段, 设置成功
  hsetnx user gender male
  (integer) 1
  
  hvals user
  1) "1001"
  2) "zhangsan"
  3) "18"
  4) "male"
  
  # 3、从hash取出一个字段的值
  hget user id
  "1001"
  
  hget user name
  "zhangsan"
  
  # 4、检查hash中是否存在某个字段, 返回0表示不存在, 返回1表示存在
  hexists user id
  (integer) 1
  
  hexists user salary
  (integer) 0
  
  # 5、列出hash中所有的字段
  hkeys user
  1) "id"
  2) "name"
  3) "age"
  
  # 6、列出hash中所有的值
  hvals user
  1) "1002"
  2) "zhangsan"
  3) "18"
  
  # 7、为hash中某个字段的值增加/减少指定数值, 返回修改后的值
  # (1) 把user的id字段的值增加22
  hincrby user id 22
  (integer) 1024
  
  # (2)把user的id字段的值减少1
  hincrby user id -1
  (integer) 1023
  ```

- 数据结构

  - hash底层数据结构有两种：```ziplist```（压缩列表）、```hashtable```（哈希表）
  - 当hash中的```field-value```长度较短且个数较少时，使用```ziplist```，否则使用```hashtable```

#### 4、Zset

- Zset为```有序集合```，与普通集合Set相似，Zset中没有重复元素

- 不同之处：

  - Zset的每个元素都关联了一个评分（```score```），用于对Zset中的元素从低到高进行排序
  - Zset中元素不能重复，但是```score```可以重复

- 常用命令

  ```shell
  # 1、添加元素
  # 格式: zadd key score1 value1 score2 value2 [...]
  zadd student 1 zhangsan 2 lisi 6 wangwu 5 zhaoliu 7 mike
  (integer) 5
  
  # 2、查看Zset中元素的值
  # (1) 仅查看元素
  zrange student 0 -1
  1) "zhangsan"
  2) "lisi"
  3) "zhaoliu"
  4) "wangwu"
  5) "mike"
  
  # (2) 查看元素, 带上分数
  zrange student 0 -1 withscores
   1) "zhangsan"
   2) "1"
   3) "lisi"
   4) "2"
   5) "zhaoliu"
   6) "5"
   7) "wangwu"
   8) "6"
   9) "mike"
  10) "7"
  
  # 3、按照score范围查找Zset中的元素
  # 查找评分在1-3分的元素, 带上分数
  zrangebyscore student 1 3 withscores
  1) "zhangsan"
  2) "1"
  3) "lisi"
  4) "2"
  
  # 4、按照score范围查找Zset中的元素, 倒序排列
  # 查找评分在5-1分的元素, 倒序排列, 带上分数
  zrevrangebyscore student 5 1 withscores
  1) "zhaoliu"
  2) "5"
  3) "lisi"
  4) "2"
  5) "zhangsan"
  6) "1"
  
  # 5、增加/减少Zset中元素关联的score, 返回修改后的score
  # (1) 使mike的score增加1
  zincrby student 1 mike
  "8"
  
  # (2) 使mike的score减少2
  zincrby student -2 mike
  "6"
  
  # 6、移除Zset中指定元素
  zrem student mike
  (integer) 1
  
  zrange student 0 -1 withscores
  1) "zhangsan"
  2) "1"
  3) "lisi"
  4) "2"
  5) "zhaoliu"
  6) "5"
  7) "wangwu"
  8) "6"
  
  # 7、统计Zset中指定score范围内的元素个数
  zcount student 1 6
  (integer) 4
  
  # 8、查询指定元素在Zset中的排名(从0开始), 如果元素不存在, 则返回null
  zrank student mike
  (nil)
  
  zrank student wangwu
  (integer) 3
  ```

- 数据结构

  - Zset底层使用了两个数据结构：```hash```、```跳跃表```
  - hash的作用是关联元素（```member```）和评分（```score```），保证元素不重复，且通过元素可以找到评分
  - 跳跃表的作用是给元素排序，根据评分获取元素列表

#### 5、Bitmaps

#### 6、HyperLogLog

#### 7、Geospatial



### 四、Redis6配置文件

### 五、Redis6发布与订阅

### 六、Jedis

### 七、SpringBoot整合Redis6



### 八、Redis6的事务操作

#### 1、Redis事务的定义

#### 2、Multi、Exec、discard

#### 3、事务的错误处理

#### 4、事务冲突的问题

#### 5、Redis事务三特性

#### 6、Redis秒杀案例



### 九、Redis6的持久化

### 十、Redis6的主从复制

### 十一、Redis6集群



### 十二、Redis6应用问题解决

#### 1、缓存穿透

#### 2、缓存击穿

#### 3、缓存雪崩

#### 4、分布式锁

### 十三、Redis6新功能

#### 1、ACL

#### 2、IO多线程

#### 3、工具支持Cluster

#### 4、Redis新功能持续关注
