# Rdb 快照备份
## 操作
### 保存： 阻塞 + fork
 - SAVE 服务器阻塞
 - [[BGSAVE]] fork 出一个子进程，子进程会执行『将内存中的数据以 RDB 格式保存到磁盘中』这一过程

BGSAVE 禁止同时 BGSAVE || SAVE（防竞争） BGWRITEAOF（性能，延后）

### 载入： dump.rdb
拷贝redis备份文件（dump.rdb）到 /usr/local/redis/bin目录下 重启服务


### 定期保存 (触发)
`save nSeconds updateTimes`
满足其中一个就会触发；
默认
```
save 900 1
save 300 1
save 60 10000
```
serveCron 100ms 定期检查 这些条件

#### 相关参数 
 - saveParams 自动保存参数 [[redisServer]]->saveParams[n]
 - dirty  上次更新后又操作了多少次
 - lastsave 上次rdb时间


## RDB 文件结构
压缩二进制文件

| REDIS | db_version | databases    | EOF | check_sum |
|-------|------------|--------------|-----|-----------|
| REDIS | rdb版本号  | 各数据库数据 | EOF | check_sum |

databases:
 selectdb dbnums (1~5bytes) pairs
 
pairs:
 TKV
 expire ms TKV

V/ value:
 encoding integer
 encoding len|rawStr
 encoding LZF|compressed_len|origin_len|compress_str
 encoding container_size| (LV)or (LKLV) or(LKScore)...

### 特殊
#### inset ziplist 都会转字符串
## 工具
redis-check-dump