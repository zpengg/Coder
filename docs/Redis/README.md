# Redis
## 用途 
[[数据类型]]

## 对象系统
* type, *ptr [[对象系统]][[类型检查]]
* encodiong [[编码方式]]
* refCount [[引用计数]]
* [[lru]]

## 服务器 
* [[redisServer]] 服务端
    * [[serverCron]]
* [[redisDb]]
    * redisDb.dict [[键空间]]
    * redisDb.[[expire]] 过期键维护 
* [[事件]]
* [[客户端]]
* [[命令执行过程]]
* [[主动淘汰策略]]
* 持久化
    * [[rdb]] 子进程
    * [[aof]] aof重写, 多线程 ,新文件接受新请求

## 多机数据库
 - [[主从复制]]
 - [[哨兵]] 
 - [[集群]]

## 其他
 - 多机通信：发布订阅消息 [[pubsub]]
 - 分布式系统一致性协议 [[raft]] [[gossip]]
 - [[事务]]
 - slowlog 慢查询
 - monitor 监视器

## 应用
[[CacheAside]]
[[雪崩]]
[[击穿]]
[[穿透]]
[[面试突击]]