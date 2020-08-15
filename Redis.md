# Redis-
## NOSQL概述  
单机MYSQL——Memcached(缓存)+MYSQL+垂直拆分——分库分表+水平拆分+MYSQL集群  
很多的数据类型包括用户的个人信息、社交网络、地理位置，这些数据类型的存储不需要一个固定的格式。  
NOSQL特点：  
1.方便扩展(数据之间没有关系，很好扩展！）  
2.大数据量高性能（Redis一秒写8万次，读取11万，NOSQL的缓存记录，是一种细粒度的缓存，性能会比较高！）  
3.数据类型是多样性的  
——不仅仅是数据  
——没有固定的查询语言  
——键值对存储、列存储、文档存储、图形数据库（社交关系）  
——最终一致性  
——CAP定理和BASE理论    

## NOSQL四大分类
KV键值对：  
·新浪：Redis  
·美团：Redis+Tair  
·阿里、百度：Redis+memcache  
文档型数据集（bson格式）：  
·MongoDB(是一个基于分布式文件存储的数据库，主要用来处理大量的文档，是一个介于关系型数据库和非关系型数据库中的中间产品，最像关系型数据库的）  
·ConthDB  
列存储数据库：  
·Hbase  
·分布式文件系统  
图关系数据库：  
·Neo4j  
·Infinite Graph  

## Redis概述
Remote Dictionary Server即远程字典服务：由一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库、并提供多种语言的API。是当下最热门的NOSQL技术之一。  
作用：  
1.内存存储、持久化（rdb、aof）  
2.效率高，可以用于高速缓存  
3.发布订阅系统  
4.地图信息分析  
5.计数器、计时器  
特性：  
1.多样的数据类型  
2.持久化  
3.集群  
4.事务  
*****Redis推荐都是在Linux服务器上搭建****  

redis benchmark(性能测试工具） 性能测试：  
redis -benchmark -h localhost -p 6379 -c 100 -n 100000  
查看redis进程是否在Linux服务器运行：  
ps -ef|grep redis  

****Based Knowledge****   
redis有16个数据库，默认使用第0个数据库，可以使用select来切换数据库：select n  
DBSIZE——查看DB大小  keys *：查看当前数据库所有的key  flushall/flushdb：清空全部数据库/清空当前数据库  
！！！redis是单线程的，redis是基于内存操作的，CPU不是redis的性能瓶颈，redis的瓶颈是根据机器的内存和网络带宽，既然可以用单线程实现，所以就使用单线程了...  
Redis为什么单线程还这么快？？？  
1.高性能的服务器不一定是多线程的，这是误区哦！  
2.多线程不一定比单线程效率高！（CPU上下文会有切换）  
核心：redis是将所有的数据全部放在内存中的，所以使用单线程操作效率是较高的；对于内存系统来说，没有上下文切换效率就是高的。在多次读写下都是在一个CPU上进行，这对于内存系统来说性能是高的。  


## Redis五大数据类型
1.Redis-key:    
set name alex  
get name  
EXISTS name  
set age 20  
keys *  
设置过期时间：EXPIRE name 10  查看剩余时间:ttl name  
move name 1  
type name 查看当前key的类型  
2.String:  
set key1 v1  
APPEND key1 "hello":追加字符串，若不存在则新创建
STRLEN key1  
APPEND key1 ",lian"  
get key1
****************  
set views 0  
get views  
incr views   自增1  
incr views  
get views  
decr views  自减1  
get views  
INCRBY views 10 按步长增长   
DECRBY views 5 按步长减少  
****************  
字符串范围 range  
GETRANGE key1 0 3  
GETRANGE key1 0 -1  
SETRANGE key1 1 xx  
setex key2 30 "hello"  
ttl key2  
setnx key3 "redis"  :在分布式锁中经常使用  
****************
批量获取值  
mset k1 v1 k2 v2 k3 v3  
keys *
mget k1 k2 k3  
msetnx k1 v1 k4 v4  
****************
对象  user:{id}:{field}
mset user:1:name zhangsan user:1:age 20  
mget user:1:name user:1:age
****************
getset 先get再set  
getset db redis  如果不存在则返回nil；若已存在则返回原值再用新值更新
get db  
getset db mongodb  
get db

3.List:   
在redis里将list玩儿成栈、队列  
LPUSH list one    将一个值或多个值插入列表头部
LPUSH list two  
LPUSH list three  
LRANGE list 0 -1  
RPUSH list four  将一个值插入列表尾部
LRANGE list 0 -1  
Lpop list  
Rpop list
***************  
lindex list 1  
lindex list 0  通过下标获取List中的某一个值  
Llen list  
移除指定的值：  
lrem list 1 one 
lrem list 2 three  
ltrim list 1 2  截断操作  
rpoplpush list otherlist  移除列表最后一个元素并将其移动到新的列表中otherlist  
***************  
lpush list value  
lset list 0 item  
lrange list 0 -1  
***************  
lpush mylist "world"  
linsert mylist before "world" "other"  
linsert mylist after "other" "new"  
4.Set:  
sadd myset "hello"  
sadd myset "world"  
SMEMBERS  
SISMEMBER myset "hello"  
scard myset  获取集合中的元素个数  
srem myset "hello"  
SRANDMEMBER myset 随机抽选集合中的元素  
spop myset 随机移除一个元素  
****************  
sadd myset1 "hello"
sadd myset2 "world"  
smove myset1 myset2 "hello"  移动指定元素到另一个集合中  
差集：SDIFF myset1 myset2  
交集: SINTER myset1 myset2  
并集：SUNION myset1 myset2  
5.Hash:  
key-Map  
hset myhash field1 lian  
hget myhash field1  
hmset myhash field1 hello field2 world  
hmget myhash field1 field2  
hgetall myhash  
hdel myhash field1 删除hash指定的key字段  
HEXISTS myhash field1  
hkeys myhash  
hvals myhash  
HINCREBY myhash field2 1  
hsetnx myhash field2 world  
hash更适合对象存储，Strinf更适合字符串存储 
6.Zset:  
有序集合  
zadd myset 1 one   
zadd myset 2 two 3 three  
ZRANGE myset 0 -1  
******************  
zadd salary 2500 xiaohong  
zadd salary 5000 zhangsan  
zadd salary 500 kaungshen  
ZRANEGBYSCORE salary -inf +inf  排序  
ZRANGEBYSCORE salary -inf +inf withscores  
zrem salary xiaohong  
zcount myset 1 3  
## 三种特殊数据类型
1.geospatial:  
地理位置  
推算地理位置信息、两地之间的距离、方圆几里的人  
geoadd china:city 116.40 39.90 beijing  
geoadd china:city 121.47 31.23 shanghai  （维度、经度、名称）
geoadd china:city 106.50 29.53 chongqing 114.05 22.52 shenzhen  
GEOPOS china:city beijing  获取指定城市的经度和纬度  
GEODIST china:city beijing shanghai  km  返回两个给定位置之间的距离   
georadius china:city 110 30 500 km withdist(withcoord) 以给定的经纬度为中心，找出某一半径内的元素  
georadiusbymember china:city shanghai 400 km  找出位于指定范围内的元素，中心点是由给定的位置元素决定  
geohash  china:city beijing chongqing 返回一个或多个位置元素的geobash表示  
****GEO的底层实现原理就是Zset，可以使用Zset命令来操作geo****  

2.hyperloglog:  
基数（不重复的元素）  
PFadd mykey a b c d e f   
PFcount mykey  
PFadd mykey1 i j m k n b  
PFMERGE mykey3 mykey mykey1  
PFcount mykey3  

3.bitmaps:  
位存储  
setbit sign 0 1  
setbit sign 1 0  
setbit sign 2 0  
...  (打卡周一到周日)  
查看某一天是否打卡  
getbit sign 3  
统计打卡的天数  
bitcount sign  


