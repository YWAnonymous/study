# Redis资料

## 1、Nosql概述

> nosql简介

not only sql  不仅仅是sql

泛指非关系型的数据库

> 3v+3高

大数据时代的3V：主要是描述问题的  1、海量Volume  2、多样Variety  3、 实时Velocity

大数据时代的3高：主要是对程序的要求 1、高并发   2、高可扩 3、高性能

> Nosql的四大分类

kV键值：redis
文档型数据库(Bson)：MongoDB
列存储数据库
图关系数据库

> CAP

Consistency(强一致性)
Availbility(可用性)
Partition tolerance(分区容错性)(必须实现)

CA    传统oracle数据库
AP	  大多数网站架构的选择
CP    redis、mongodb 	

## 2、Redis 概述

> Redis是什么	

REmote DIctionary Server 远程字典服务器

是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库， 并提供多种语言的API。

Redis推荐都是在Linux服务器上搭建的，我们是基于Linux学习！

```markdown
默认装16个库，select 切换数据库（从0开始）
三个特点：
    1.内存存储和持有化
    2.key-value,还提供list，set，zset，hash等数据结构的存储
    3.备份，即master-slave模式数据备份
```

> Redis基础命令

```shell
select 3 #切换数据库
dbsize   #查看数据库大小
keys *   #查看数据库所有的key
flushdb  #清除当前数据库 
flushall #清除全部数据库的内容 
```

## 3、五大数据类型

### Redis-key

```shell
set key key    # set key
get key        # get key
keys *         # 查看所有key
exists key     # 判断key是否存在
	若key存在返回1，否则返回0
expire key 10  # 设置key的过期时间，单位是秒  注意：key必须存在才能设置
	设置成功返回1，失败返回0
ttl key        # 查看key剩余时间
	返回-1表示key存在并且没有设置过期时间
	返回-2表示key不存在或过期
type key       # 查看key的类型
```

### String(字符串)

```shell
set key zhangsan
get key
keys *
exists key
append key aa    # 追加字符串，如果key不存在，相当于set key
strlen key       # 获取字符串长度
#######################################################################

set views 0
incr views      # 自增1
decr views      # 自减1
incrby views 10 # 设置步长，增加
decrby views 10 # 设置步长，减少

set name abc
get name
getrange name 0 -1   # 截取字符串 ，[0,-1]是全部字符串，相等于get key
setrange name 0 -1   # 替换指定位置开始的字符串！

# setex (set with expire)     # 设置过期时间
# setnx (set if not exist)    # 不存在在设置 （在分布式锁中会常常使用！）

setex key3 30 "hello"         # 设置key3 的值为 hello,30秒后过期
ttl keys
setnx mykey "redis" # 如果mykey 不存在，创建mykey,并且赋值
get mykey

mset k1 v1 k2 v2 k3 v3        # 同时设置多个值
mget k1 k2 k3                 # 同时取多个值
msetnx k1 v1 k4 v4 # msetnx 是一个原子性的操作，要么一起成功，要么一起失败！

# 对象
set user:1 {name:zhangsan,age:3} # 设置一个user:1 对象 值为 json字符来保存一个对象！
get user:1
>>>{name:zhangsan,age:3}   #然后解析json串
# 这里的key是一个巧妙的设计： user:{id}:{filed} , 如此设计在Redis中是完全OK了！
mset user:1:name zhangsan user:1:age 2
mget user:1:name user:1:age

getset db redis # 如果不存在值，则返回 nil
getset db mongodb # 如果存在值，获取原来的值，并设置新的值
```

### List(列表)

> 在redis里面，我们可以把list玩成 ，栈、队列、阻塞队列！
>
> 所有的list命令都是用l开头的，Redis不区分大小命令

```shell
lpush list one         # 将一个值或者多个值，插入到列表头部 （左）
lrange list 0 -1       # 获取list中值
rpush list one         # 将一个值或者多个值，插入到列表位部 （右）

lpop list              # 移除list的第一个元素
rpop list              # 移除list的最后一个元素
lindex list 1          # 通过下标获得 list 中的某一个值！
llen list              # 返回列表的长度

lrem list 1 one        # 移除list集合中指定个数的value，精确匹配
ltrim mylist 1 2 # 通过下标截取指定的长度，这个list已经被改变了，截断了只剩下截取的元素！

rpoplpush mylist myotherlist # 移除列表的最后一个元素，将他移动到新的列表中！

EXISTS list # 判断这个列表是否存在
lset list 0 item # 如果不存在列表我们去更新就会报错
lset list 0 item # 如果存在，更新当前下标的值
lset list 1 other # 如果不存在，则会报错！
	(error) ERR index out of range
linsert key before|after 指定元素 other # 将某个具体的value插入到列把你中某个元素的前面或者后面！

```

+ 消息队列 （Lpush Rpop）， 栈（ Lpush Lpop）！

### Set(集合)

set中的值是不能重读的！

```shell
sadd myset hello           # set集合中添加数据
smembers myset             # 查看指定set的所有值
sismember myset hello      # 判断某一个值是不是在set集合中！
scard myset                # 获取set集合中的内容元素个数！

srem myset hello           # 移除set集合中的指定元素
set 无序不重复集合。抽随机！
srandmember myset          # 随机抽选出一个元素  
srandmember myset 2        # 随机抽选出指定个数的元素

spop myset                 # 随机删除一些set集合中的元素！

smove myset myset2 one     # 将一个指定的值，移动到另外一个set集合！
数字集合类：
    - 差集 SDIFF
    - 交集
    - 并集
sdiff key1 key2             # 差集
sinter key1 key2            # 交集 共同好友就可以这样实现
sunion key1 key2            # 并集
```

微博，A用户将所有关注的人放在一个set集合中！将它的粉丝也放在一个集合中！ 

共同关注，共同爱好，二度好友，推荐好友！

### Hash(哈希)

> Map集合，key-map! 值是一个map集合！ 本质和String类型没有太大区别，还是一个简单的 key-vlaue！

```shell
hset myhash field one        # set一个具体 key-vlaue
hget myhash field            # 获取一个字段值
hmset myhash field1 hello field2 world # set多个 key-vlaue
hmget myhash field1 field2   # 获取多个值
hgetall myhash               # 获取全部的数据
hdel myhash field1           # 删除hash指定key字段！对应的value值也就消失了！
hlen myhash                  # 获取hash表的字段数量！

hexists myhash field         # 判断hash中指定字段是否存在！
hkeys myhash                 # 只获得所有field
hvals myhash                 # 只获得所有value

hsetnx myhash field4 hello   # 如果不存在则可以设置
hsetnx myhash field4 world   # 如果存在则不能设置
```

hash变更的数据 user name age,尤其是是用户信息之类的，经常变动的信息！ hash 更适合于对象的 存储，String更加适合字符串存储！

### ZSet(有序集合)

在set的基础上，增加了一个值，set k1 v1       zset k1 score v1

```SHELL
zadd myset 1 one              # 添加一个值
zadd myset 2 two 3 three      # 添加多个值

zrange myset 0 -1             # 获取zset中的数据

zadd salary 2500 xiaosan
zadd salary 500 zhangsan
zadd salary 1000 lisi
zrangebyscore salary -inf +inf   # 显示全部的用户 从小到大！（-inf 负无穷  +inf 正无穷）
zrangebyscore salary -inf +inf withscores # 显示全部的用户并且附带成绩
zrangebyscore salary -inf 2500 withscores # 显示工资小于2500员工的升序排序！

zrevrange salary 0 -1         # 从大到进行排序！

zrem salary xiaohong          # 移除有序集合中的指定元素

zcard salary                  # 获取有序集合中的个数
zcount myset 1 3              # 获取指定区间的成员数量！
```

案例思路：set 排序 存储班级成绩表，工资表排序！ 

普通消息，1， 重要消息 2，带权重进行判断！

## 4、Linux安装

```shell
# 1、官网下载安装包
redis-5.0.10.tar.gz
# 2、解压缩
tar -zxvf redis-5.0.10.tar.gz
# 3、安装基础环境：redis使用c++编写
yum install gcc-c++
# 4、进入压缩之后文件编译redis
make && make install

# 5、redis的默认安装路径 /usr/local/bin
# 将安装目录下配置文件redis.conf拷贝到自己的目录下
[/usr/local/bin/myconfig]# cp /opt/redis-5.0.10/redis.conf myconfig

# 5、修改配置文件redis.conf  --->开启redis后台守护进程
daemonize yes

# 6、启动redis服务
redis-server myconfig/redis.conf
# 7、可以使用ps -ef|grep redis查看
# 8、连接redis-cli
redis-cli -p 6379

# 9、退出redis服务 shutdown
退出exit
```

### make的补充说明

```shell
# 也可以这样写
-加上关键字 PREFIX
make PREFIX=/usr/local/redis install
PREFIX= 作用是编译的时候用于指定程序存放的路径。比如我们现在就是指定了redis必须存放在/usr/local/redis目录。假设不添加该关键字Linux会将可执行文件存放在/usr/local/bin目录，

#库文件会存放在/usr/local/lib目录。配置文件会存放在/usr/local/etc目录。其他的资源文件会存放在usr/local/share目录。这里指定号目录也方便后续的卸载，后续直接rm -rf /usr/local/redis 即可删除redis。

首先我们要知道redis 需要使用内存分配器的， make  MALLOC=jemalloc  就是指定内存分配器为 jemalloc ，make MALLOC=libc 就是指定内存分配器为 libc ，这个是有安全隐患的，jemalloc 内存分配器在实践中处理内存碎片是要比libc 好的，而且在README.md 文档也说明到了，jemalloc内存分配器也是包含在源码包里面的，可以在deps  目录下看到 jemalloc 目录。
```

## 5、==Redis持久化==

Redis 是内存数据库，如果不将内存中的数据库状态保存到磁盘，那么一旦服务器进程退出，服务器中 的数据库状态也会消失。所以 Redis 提供了持久化功能！

### RDB（Redis DataBase）

```markdown
在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的Snapshot快照，它恢复时是将快照文件直接读到内存里。

Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何IO操作的。这就确保了极高的性能。如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。我们默认的就是RDB，一般情况下不需要修改这个配置！

有时候在生产环境我们会将这个文件进行备份！

rdb保存的文件是dump.rdb 都是在我们的配置文件redis.conf中快照中进行配置的！
```

==AAA==





























