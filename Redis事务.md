# Redis-
## 事务 
要么同时成功，要么同时失败：原子性  
Redis单条命令是保证原子性的，但是事务不保证原子性！  
Redis事务本质：一组命令的集合  
一个事务中的所有命令都会被序列化，在事务执行过程中，会被按照顺序执行。  
一次性、顺序性、排他性  
Redis事务没有隔离级别的概念，所有命令在事务中并没有直接执行，只有发起执行命令的时候才会被执行。  
redis事务：  
·开启事务（）multi  
·命令入队（）  
·执行事务（）  exec
set k1 v1  
get k1  
set k2 v2  
get k2  
exec 执行事务  
放弃事务：  discard  
*****************
1.编译型异常 ：事务中所有的命令都不会执行 
2.运行时异常  ：如果事务队列中存在语法性，那么执行命令的时候，其他命令是可以正常执行的，错误命令抛出异常  
*****************  
监控：  
悲观锁：  
·很悲观，什么时候都会出问题，无论做什么都会加锁！
乐观锁：  
·很乐观，认为什么时候都不会出现问题，所以不会上锁！更新数据的时候去判断一下，在此期间是否有人修改过这个数据  
正常执行成功：  
set money 100  
set out 0  
watch money  监视money对象
multi  
decrby money 20  
incrby money 10  
exec  
测试多线程修改值，使用watch可以当做redis的乐观锁操作！  
（执行事务过程中，另一个线程更新了值，则会导致事务执行失败）  
解决办法：先unwatch再重新watch  

## Jedis  
使用Java来操作redis,Jedis是官方推荐的Java连接工具，使用java操作redis中间件   
Jedis jedis=new Jedis("127.0.0.1",6379)  
System.out.println(jedis.ping());
********************
事务:  
public static void main(String[] args){
    Jedis jedis=new Jedis("127.0.0.1",6379)  
    //开启事务  
      Transaction multi=jedis.multi()  
      JSONObject jsonobject=new JSONObject()  
      jsonobject.put("hello","world")  
      jsonobject.put("name","lian")  
      String result=jsonObject.toJSONString()  
      try{  
        multi.set("user1",result)  
        multi.set("user2",result)  
        mutli.exec()
       }catch(Exception e){  
          multi.discard()  
       }  
       
      jedis.close()

## SpringBoot整合  
编写自己的RedisTemplate  
## Redis.conf 详解  
启动的时候就通过配置文件来启动  
1.配置文件对大小写不敏感  
2.绑定的IP bind 127.0.0.1  protected-mode yes 保护模式   port 6379通用端口  daemonize yes以守护进程的方式运行  
redis是内存数据库，如果没有持久化，那么数据断电及失！  
maxclients 设置能连接上的redis的最大客户端的数量  
"appendonly.aof" 持久化文件的名字

## Redis持久化  
·RDB（Redis Database）
在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的snapshot快照，它恢复时是将快照文件直接读到内存里  
Redis会单独创建一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能。  
如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。  
优点：  
1.适合大规模的数据恢复  
2.对数据的完整性要求不高  
缺点：  
1.需要一定的时间间隔进程操作，如果redis意外宕机了，这个最后一次修改数据就没有了  
2.fork进程的时候，会占用一定的内存空间   

·AOF（Append Only File）将所有命令都记录下来，history,恢复的时候把所有命令重新执行一遍  

将appendonly 改为yes就启动了AOF
优点：  
1.每一次修改都同步，文件的完整会更加好  
2.每秒同步一次，可能会丢失一秒的数据  
3.从不同步，效率最高的！  
缺点：  
1.相对于数据文件来说，aof远远大于rdb,修复的速度也比rdb慢  
2.aof的效率也要比rdb慢


## Redis 发布订阅  
Redis发布订阅（pub/sub）是一种消息通信模式：发送者（pub）发送消息，订阅者（sub）接收消息  
Redis客户端可以订阅任意数量的频道
client1:  
SUBSCRIBEE aze  
client2:  
PUBLISH aze "hello"  
PUBLISH aze "world"  


  
## Redis 主从复制
主从复制，是指将一台Redis服务器的数据，复制到其他的Redis服务器，前者称为主节点，后者称为从节点；数据的复制是单向的，只能从主节点到副节点，Master、Slave  
减缓服务器压力，架构中经常使用  
作用：  
1.数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式  
2.故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复；实际上是一种服务的冗余；  
3.负载均衡：在主从复制的基础上，配合读写分离，可以由主节点提供写服务，由从节点提供读服务，分担服务器负载；尤其是在写少读多的场景下，通过多个从节点分担读负载，可以大大提高Redis服务器的并发量  
4.高可用基石：除了上述作用以外，主从复制还是哨兵和集群能够实施的基础，因此说主从复制是redis高可用的基础。  
1主2从
redis79.conf  redis80.conf  redis81.conf  
更改：dump6379.rdb
更改redis80.conf  :端口改为6380  logfile="6380.log"  dump6380.rdb
更改redis81.conf  :端口改为6381  logfile="6381.log"  dump6381.rdb(还要改pid名字)
配置从机：  
一主（79）二从（80、81）  
SLAVEOF 127.0.0.1 6379  
在主机中查看:info repliaction  
主机可以写可以读，从机不能写只能读  
·全量复制：Slave服务在接收到数据库文件数据后，将其存盘并加载到内存中  
·增量复制：Master继续将新的所有收集到的修改命令依次传给slave，完成同步  

## 哨兵模式（自动选主机的模式）
哨兵模式是一种特殊的模式，首先redis提供了哨兵的命令，哨兵是一个独立的进程,作为进程，它会独立运行。其原理是哨兵通过发送命令，等待redis服务器响应，从而监控运行的多个redis实例。  
然而一个哨兵进程对redis服务器进行监控，可能会出现问题，为此，我们可以使用多个哨兵进行监控。各个哨兵之间还会进行监控，这样就形成了多哨兵模式。  
vim sentinel.conf  
sentinel monitor myredis(被监控的名称） 127.0.0.1（host） 6379(port) 1(主机挂了，slave投票按让谁接替称为主机)  配置哨兵配置文件  
redis-sentinel kconfig/sentinel.conf  
如果Master节点断开了，这个时候就会从从机中随机选择一个服务器（这里面有一个投票算法）  
缺点：Redis不好在线扩容，实现哨兵模式的配置比较麻烦，里面有很多选择！


## Redis 缓存穿透和雪崩 
Redis缓存的使用，极大的提升了应用程序的性能和效率，特别是数据查询方面。但同时,它也带来了一些问题。其中， 最要害的问题，就是数据的一致性问题，从严格意义上讲，这个问题无解。如果对数据的一致性要求  
很高，那么就不能使用缓存。  

缓存穿透：用户想要查询一个数据，发现redis内存数据库没有，也就是缓存没有命中，于是向持久层数据库查询。发现也没有，于是本次查询失败。当用户很多的时候，缓存都没有命中，于是都去请求了持久层数据库。  
这会给持久层数据库造成很大的压力，这时候就相当于出现了缓存穿透。（查不到）  
缓存击穿：量太大，缓存过期！  

双十一：停掉一些服务，保证主要服务可用！  
解决方案：  
1.redis高可用  
2.限流降级  
3.数据预热  
数据预热的含义就是在正式部署之前，我先把可能的数据先预先访问一遍，这样部分可能大量访问的数据就会加载到缓存中。在即将发生大并发访问前手动触发加载缓存不同的key，设置不同的过期时间，让缓存失效的时间点尽量均匀。  

