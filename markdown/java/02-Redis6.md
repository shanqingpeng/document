### 一、NoSQL简介

### 二、Redis6简介和安装

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

#### 4、Hash

#### 4、Zset

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
