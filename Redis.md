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
4.Set:  
5.Hash:  
6.Zset:  

## 三种特殊数据类型
1.geospatial:  
2.hyperloglog:  
3.bitmaps:  

