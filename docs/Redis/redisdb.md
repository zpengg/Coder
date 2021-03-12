# RdisDb 数据库
## redisDb
每个数据库都是一个 redisDb 结构体：
## 内存结构
```
+---------+                         +------------+
| redisDb |                     +-> | ListObject |
+---------+    +------------+   |   +------------+
|  dict   | -> |  StringObj | --+
+---------+    +------------+       +------------+
| expires |    |  StringObj | ----> | HashObject |
+---------+    +------------+       +------------+
    |          |  StringObj | --+
    |          +------------+   |   +------------+
    |                           +-> | StringObj  |
    |                               +------------+
    |
    |       +------------+    +-------------+
    +---->  |  StringObj | -> |    long     |
            +------------+    +-------------+
            |  StringObj | -> |    long     |
            +------------+    +-------------+
```
## code
```c
typedef struct redisDb {
        dict *dict;                 /* 据库的键空间 keyspace */
        dict *expires;              /* 过期时间值dict */
        dict *blocking_keys;        /* 客户端阻塞等待的 key 集合 (BLPOP)*/
        dict *ready_keys;           /* 已就绪的阻塞 key 集合 (PUSH) */
        dict *watched_keys;         /* 在事务中监控受监控的 key 集合 */
        int id;                     /* 数据库 ID */
        long long avg_ttl;          /* 平均 TTL, just for stats */
        unsigned long expires_cursor; /* 过期检测指针 */
        list *defrag_later;         /* 内存碎片回收列表 */
    } redisDb;
```



