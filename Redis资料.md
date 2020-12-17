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

#### 1、特点

```markdown
在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的Snapshot快照，它恢复时是将快照文件直接读到内存里。

Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何IO操作的。这就确保了极高的性能。如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。我们默认的就是RDB，一般情况下不需要修改这个配置！
```

==rdb保存的文件是dump.rdb== 都是在我们的配置文件redis.conf中快照中进行配置的！

#### 2、RDB生成方式

+ 客户端方式: BGSAVE 和 SAVE指令

```markdown
# 1.客户端方式之BGSAVE
- a.客户端可以使用BGSAVE命令来创建一个快照,当接收到客户端的BGSAVE命令时,redis会调用fork¹来创建一个子进程,然后子进程负责将快照写入磁盘中,而父进程则继续处理命令请求。

# 2.客户端方式之SAVE
- b.由客户端还可以使用SAVE命令来创建一个快照,接收到SAVE命令的redis服务器在快照创建完毕之前将不再响应任何其他的命令
```

**注意: SAVE命令并不常用,使用SAVE命令在快照创建完毕之前,redis处于阻塞状态,无法对外服务**

+ 服务器配置方式之满足配置自动触发

```markdown
# 3.服务器配置方式之满足配置自动触发
- 如果用户在redis.conf中设置了save配置选项,redis会在save选项条件满足之后自动触发一次BGSAVE命令,如果设置多个save配置选项,当任意一个save配置选项条件满足,redis也会触发一次BGSAVE命令

在redis.conf中SNAPSHOTTING模块,达到其中一个条件
# 如果900s内，如果至少有一个1 key进行了修改，我们及进行持久化操作
save 900 1
# 如果300s内，如果至少10 key进行了修改，我们及进行持久化操作
save 300 10
# 如果60s内，如果至少10000 key进行了修改，我们及进行持久化操作
save 60 10000
```

+ shutdown指令

```markdown
# 4.服务器接收客户端shutdown指令
- 当redis通过shutdown指令接收到关闭服务器的请求时,会执行一个save命令,阻塞所有的客户端,不再执行客户端执行发送的任何命令,并且在save命令执行完毕之后关闭服务器
```

+ flushall指令

```markdown
# 5、flushall
```

#### 3、恢复RDB文件

1、只需要将rdb文件放在我们redis启动目录就可以，redis启动的时候会自动检查dump.rdb 恢复其中 的数据！ 2、查看需要存在的位置

2、查看需要存在的位置

```shell
127.0.0.1:6379> config get dir
1) "dir"
2) "/usr/local/bin" # 如果在这个目录下存在 dump.rdb 文件，启动就会自动恢复其中的数据
```

#### 4、配置生成快照名称和位置

```markdown
# 在配置文件redis.conf中配置
# 1.修改生成快照名称
- dbfilename dump.rdb

# 2.修改生成位置
- dir ./
```

### AOF（Append Only File）

#### 1、特点

```markdown
- 这种方式可以将所有客户端执行的写命令记录到日志文件中,AOF持久化会将被执行的写命令写到AOF的文件末尾,以此来记录数据发生的变化,因此只要redis从头到尾执行一次AOF文件所包含的所有写命令,就可以恢复AOF文件的记录的数据集.
```

==Aof保存的是 appendonly.aof 文件==

#### 2、开启AOF持久化

> 在redis的默认配置中AOF持久化机制是没有开启的，需要在配置中开启

```markdown
# 1.开启AOF持久化
- a.修改 appendonly yes 开启持久化
- b.修改 appendfilename "appendonly.aof" 指定生成文件名称
```

#### 3、日志追加频率

```markdown
# 1.always 【谨慎使用】
- 说明: 每个redis写命令都要同步写入硬盘,严重降低redis速度
- 解释: 如果用户使用了always选项,那么每个redis写命令都会被写入硬盘,从而将发生系统崩溃时出现的数据丢失减到最少;遗憾的是,因为这种同步策略需要对硬盘进行大量的写入操作,所以redis处理命令的速度会受到硬盘性能的限制;
- 注意: 转盘式硬盘在这种频率下200左右个命令/s ; 固态硬盘(SSD) 几百万个命令/s;
- 警告: 使用SSD用户请谨慎使用always选项,这种模式不断写入少量数据的做法有可能会引发严重的写入放大问题,导致将固态硬盘的寿命从原来的几年降低为几个月。

# 2.everysec 【推荐】
- 说明: 每秒执行一次同步显式的将多个写命令同步到磁盘
- 解释： 为了兼顾数据安全和写入性能,用户可以考虑使用everysec选项,让redis每秒一次的频率对AOF文件进行同步;redis每秒同步一次AOF文件时性能和不使用任何持久化特性时的性能相差无几,而通过每秒同步一次AOF文件,redis可以保证,即使系统崩溃,用户最多丢失一秒之内产生的数据。

# 3.no	【不推荐】
- 说明: 由操作系统决定何时同步 
- 解释：最后使用no选项,将完全有操作系统决定什么时候同步AOF日志文件,这个选项不会对redis性能带来影响但是系统崩溃时,会丢失不定数量的数据,另外如果用户硬盘处理写入操作不够快的话,当缓冲区被等待写入硬盘数据填满时,redis会处于阻塞状态,并导致redis的处理命令请求的速度变慢。


# 修改日志同步频率
- 修改appendfsync everysec|always|no 指定
```

#### 4、AOF文件的重写

`AOF的方式也同时带来了另一个问题。持久化文件会变的越来越大。例如我们调用incr test命令100次，文件中必须保存全部的100条命令，其实有99条都是多余的。因为要恢复数据库的状态其实文件中保存一条set test 100就够了。为了压缩aof的持久化文件Redis提供了AOF重写(ReWriter)机制。`

+ 触发重写方式

```markdown
# 1.客户端方式触发重写(bgrewriteaof)
- 执行BGREWRITEAOF命令  不会阻塞redis的服务

# 2.服务器配置方式自动触发
- 配置redis.conf中的auto-aof-rewrite-percentage选项 参加下图↓↓↓
- 如果设置auto-aof-rewrite-percentage值为100和auto-aof-rewrite-min-size 64mb,并且启用的AOF持久化时,那么当AOF文件体积大于64M,并且AOF文件的体积比上一次重写之后体积大了至少一倍(100%)时,会自动触发,如果重写过于频繁,用户可以考虑将auto-aof-rewrite-percentage设置为更大
```

+ 重写原理

```markdown
# 重写流程
- 1. redis调用fork ，现在有父子两个进程 子进程根据内存中的数据库快照，往临时文件中写入重建数据库状态的命令
- 2. 父进程继续处理client请求，除了把写命令写入到原来的aof文件中。同时把收到的写命令缓存起来。这样就能保证如果子进程重写失败的话并不会出问题。
- 3. 当子进程把快照内容写入已命令方式写到临时文件中后，子进程发信号通知父进程。然后父进程把缓存的写命令也写入到临时文件。
- 4. 现在父进程可以使用临时文件替换老的aof文件，并重命名，后面收到的写命令也开始往新的aof文件中追加。
```

### 拓展

```markdown
1、RDB 持久化方式能够在指定的时间间隔内对你的数据进行快照存储
2、AOF 持久化方式记录每次对服务器写的操作，当服务器重启的时候会重新执行这些命令来恢复原始
的数据，AOF命令以Redis 协议追加保存每次写的操作到文件末尾，Redis还能对AOF文件进行后台重
写，使得AOF文件的体积不至于过大。
3、只做缓存，如果你只希望你的数据在服务器运行的时候存在，你也可以不使用任何持久化
4、同时开启两种持久化方式
在这种情况下，当redis重启的时候会优先载入AOF文件来恢复原始的数据，因为在通常情况下AOF
文件保存的数据集要比RDB文件保存的数据集要完整。
RDB 的数据不实时，同时使用两者时服务器重启也只会找AOF文件，那要不要只使用AOF呢？作者
建议不要，因为RDB更适合用于备份数据库（AOF在不断变化不好备份），快速重启，而且不会有
AOF可能潜在的Bug，留着作为一个万一的手段。
5、性能建议
因为RDB文件只用作后备用途，建议只在Slave上持久化RDB文件，而且只要15分钟备份一次就够
了，只保留 save 900 1 这条规则。
如果Enable AOF ，好处是在最恶劣情况下也只会丢失不超过两秒数据，启动脚本较简单只load自
己的AOF文件就可以了，代价一是带来了持续的IO，二是AOF rewrite 的最后将 rewrite 过程中产
生的新数据写到新文件造成的阻塞几乎是不可避免的。只要硬盘许可，应该尽量减少AOF rewrite
的频率，AOF重写的基础大小默认值64M太小了，可以设到5G以上，默认超过原大小100%大小重
写可以改到适当的数值。
如果不Enable AOF ，仅靠 Master-Slave Repllcation 实现高可用性也可以，能省掉一大笔IO，也
减少了rewrite时带来的系统波动。代价是如果Master/Slave 同时倒掉，会丢失十几分钟的数据，
启动脚本也要比较两个 Master/Slave 中的 RDB文件，载入较新的那个，微博就是这种架构。
```

## 6、Redis主从复制

### 1、特点

主从复制，是指将一台Redis服务器的数据，复制到其他的Redis服务器。前者称为主节点 (master/leader)，后者称为从节点(slave/follower)；数据的复制是单向的，只能由主节点到从节点。Master以写为主，Slave 以读为主。

且一个主节点可以有多个从节点(或没有从节点)，但一个从节点只能有一个主节点。（）

主从复制架构仅仅用来解决数据的冗余备份,从节点仅仅用来同步数据

无法解决: 1.master节点出现故障的自动故障转移



### 2、环境配置

> 只配置从库，不用配置主库！

==默认情况下，每台Redis服务器都是主节点；== 主机可以写，从机不能写只能读！主机中的所有信息和数据，都会自动被从机保存！

```markdown
- info replication # 查看当前库的信息
# 1.准备3台机器并修改配置
- master    port 6379
- slave1    port 6380
- slave2    port 6381
# SLAVEOF host 6379   设置从机
- slaveof 127.0.0.1 6379 
```

> 复制原理

Slave 启动成功连接到 master 后会发送一个sync同步命令 Master 接到命令，启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行 完毕之后，master将传送整个数据文件到slave，并完成一次完全同步。 

全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。

 增量复制：Master 继续将新的所有收集到的修改命令依次传给slave，完成同步 

但是只要是重新连接master，一次完全同步（全量复制）将被自动执行！ 我们的数据一定可以在从机中 看到！

> 谋朝篡位 

如果主机断开了连接，我们可以使用 `SLAVEOF no one `让自己变成主机！其他的节点就可以手动连 接到最新的这个主节点（手动）！如果这个时候老大修复了，那就重新连接！

## 7、Redis哨兵机制

### 哨兵Sentinel机制

Sentinel（哨兵）是Redis 的高可用性解决方案：由一个或多个Sentinel 实例 组成的Sentinel 系统可以监视任意多个主服务器，以及这些主服务器属下的所有从服务器，并在被监视的主服务器进入下线状态时，自动将下线主服务器属下的某个从服务器升级为新的主服务器。简单的说哨兵就是带有**自动故障转移功能的主从架构**。

 谋朝篡位的自动版，能够后台监控主机是否故障，如果故障了根据投票数==自动将从库转换为主库。==

哨兵模式是一种特殊的模式，首先Redis提供了哨兵的命令，哨兵是一个独立的进程，作为进程，它会独立运行。其原理是哨兵通过发送命令，等待Redis服务器响应，从而监控运行的多个Redis实例。

这里的哨兵有两个作用

+ 通过发送命令，让Redis服务器返回监控其运行状态，包括主服务器和从服务器。 
+ 当哨兵监测到master宕机，会自动将slave切换成master，然后通过发布订阅模式通知其他的从服 务器，修改配置文件，让它们切换主机。 

然而一个哨兵进程对Redis服务器进行监控，可能会出现问题，为此，我们可以使用多个哨兵进行监控。 各个哨兵之间还会进行监控，这样就形成了==多哨兵模式==。

假设主服务器宕机，哨兵1先检测到这个结果，系统并不会马上进行failover过程，仅仅是哨兵1主观的认 为主服务器不可用，这个现象成为**主观下线**。当后面的哨兵也检测到主服务器不可用，并且数量达到一 定值时，那么哨兵之间就会进行一次投票，投票的结果由一个哨兵发起，进行failover[故障转移]操作。 切换成功后，就会通过发布订阅模式，让各个哨兵把自己监控的从服务器实现切换主机，这个过程称为 **客观下线**。

### 搭建哨兵架构

```markdown
# 1.在主节点上创建哨兵配置
- 在Master对应redis.conf同目录下新建sentinel.conf文件，名字绝对不能错；

# 2.配置哨兵，在sentinel.conf文件中填入内容：
- sentinel monitor 被监控数据库名字（自己起名字） ip port 1
- sentinel monitor myredis 127.0.0.1 6379 1

# 3.启动哨兵模式进行测试
- redis-sentinel  myconfig/sentinel.conf
	说明:这个后面的数字2,是指当有两个及以上的sentinel服务检测到master宕机，才会去执行主从切换的功能。
```

**后面的这个数字1，代表主机挂了，slave投票看让谁接替成为主机，票数最多的，就会成为主机！**

**当主机回来的时候，只能当从机了**

### 拓展sentinel.conf

```shell
# Example sentinel.conf
# 哨兵sentinel实例运行的端口 默认26379
port 26379
# 哨兵sentinel的工作目录
dir /tmp
# 哨兵sentinel监控的redis主节点的 ip port
# master-name 可以自己命名的主节点名字 只能由字母A-z、数字0-9 、这三个字符".-_"组成。
# quorum 配置多少个sentinel哨兵统一认为master主节点失联 那么这时客观上认为主节点失联了
# sentinel monitor <master-name> <ip> <redis-port> <quorum>
sentinel monitor mymaster 127.0.0.1 6379 2
# 当在Redis实例中开启了requirepass foobared 授权密码 这样所有连接Redis实例的客户端都要提供密码
# 设置哨兵sentinel 连接主从的密码 注意必须为主从设置一样的验证密码
# sentinel auth-pass <master-name> <password>
sentinel auth-pass mymaster MySUPER--secret-0123passw0rd
# 指定多少毫秒之后 主节点没有应答哨兵sentinel 此时 哨兵主观上认为主节点下线 默认30秒
# sentinel down-after-milliseconds <master-name> <milliseconds>
sentinel down-after-milliseconds mymaster 30000
# 这个配置项指定了在发生failover主备切换时最多可以有多少个slave同时对新的master进行 同步，这个数字越小，完成failover所需的时间就越长，但是如果这个数字越大，就意味着越 多的slave因为replication而不可用。可以通过将这个值设为 1 来保证每次只有一个slave 处于不能处理命令请求的状态。
# sentinel parallel-syncs <master-name> <numslaves>
sentinel parallel-syncs mymaster 1
# 故障转移的超时时间 failover-timeout 可以用在以下这些方面：
#1. 同一个sentinel对同一个master两次failover之间的间隔时间。
#2. 当一个slave从一个错误的master那里同步数据开始计算时间。直到slave被纠正为向正确的master那里同步数据时。
#3.当想要取消一个正在进行的failover所需要的时间。
#4.当进行failover时，配置所有slaves指向新的master所需的最大时间。不过，即使过了这个超时，slaves依然会被正确配置为指向master，但是就不按parallel-syncs所配置的规则来了
# 默认三分钟
# sentinel failover-timeout <master-name> <milliseconds>
sentinel failover-timeout mymaster 180000
# SCRIPTS EXECUTION
#配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。
#对于脚本的运行结果有以下规则：
#若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10
#若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。
#如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
#一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被一个SIGKILL信号终止，之后重新执行。
#通知型脚本:当sentinel有任何警告级别的事件发生时（比如说redis实例的主观失效和客观失效等等），将会去调用这个脚本，这时这个脚本应该通过邮件，SMS等方式去通知系统管理员关于系统不正常运行的信息。调用该脚本时，将传给脚本两个参数，一个是事件的类型，一个是事件的描述。如果sentinel.conf配置文件中配置了这个脚本路径，那么必须保证这个脚本存在于这个路径，并且是可执行的，否则sentinel无法正常启动成功。
#通知脚本
# shell编程
# sentinel notification-script <master-name> <script-path>
sentinel notification-script mymaster /var/redis/notify.sh
# 客户端重新配置主节点参数脚本
# 当一个master由于failover而发生改变时，这个脚本将会被调用，通知相关的客户端关于master地址已经发生改变的信息。
# 以下参数将会在调用脚本时传给脚本:
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
# 目前<state>总是“failover”,
# <role>是“leader”或者“observer”中的一个。
# 参数 from-ip, from-port, to-ip, to-port是用来和旧的master和新的master(即旧的slave)通信的
# 这个脚本应该是通用的，能被多次调用，不是针对性的。
# sentinel client-reconfig-script <master-name> <script-path>
sentinel client-reconfig-script mymaster /var/redis/reconfig.sh # 一般都是由运维来配置！
```

## 8、java操作redis

### 环境准备

```java
<!--引入jedis连接依赖-->
<dependency>
  <groupId>redis.clients</groupId>
  <artifactId>jedis</artifactId>
  <version>2.9.0</version>
</dependency>

public static void main(String[] args) {
   //1.创建jedis对象
   Jedis jedis = new Jedis("192.168.40.4", 6379);//1.redis服务必须关闭防火墙  2.redis服务必须开启远程连接
   jedis.select(0);//选择操作的库默认0号库
   //2.执行相关操作
   //....
   //3.释放资源
   jedis.close();
 }
```

## 9、springboot整合redis

Spring Boot Data(数据) Redis 中提供了**RedisTemplate和StringRedisTemplate**，其中StringRedisTemplate是RedisTemplate的子类，两个方法基本一致，不同之处主要体现在操作的数据类型不同，**RedisTemplate中的两个泛型都是Object，意味着存储的key和value都可以是一个对象，而StringRedisTemplate的两个泛型都是String，意味着StringRedisTemplate的key和value都只能是字符串。**

`注意: 使用RedisTemplate默认是将对象序列化到Redis中,所以放入的对象必须实现对象序列化接口`

### 环境准备

> 说明： 在 SpringBoot2.x 之后，原来使用的jedis 被替换为了 lettuce
>
>  jedis : 采用的直连，多个线程操作的话，是不安全的，如果想要避免不安全的，使用 jedis pool 连接 池！ 更像 BIO 模式
>
>  lettuce : 采用netty，实例可以再多个线程中进行共享，不存在线程不安全的情况！可以减少线程数据 了，更像 NIO 模式

#### 1、引入依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

#### 2、application.properties

```properties
spring.redis.host=localhost
spring.redis.port=6379
spring.redis.database=0
```

#### 3、RedisTemplate

`redistemplate默认的序列化方式是JDK序列化，也可以设置json序列化`

```java
//设置redistemplate值使用对象序列化策略
Jackson2JsonRedisSerializer
    jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class));
redisTemplate.setValueSerializer(new JdkSerializationRedisSerializer());//使用JDK序列化
//redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);  //使用json序列化
```







