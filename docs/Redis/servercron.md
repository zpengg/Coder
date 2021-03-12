# serverCron 100ms 
100 ms interval
 - 更新服务器时间缓存 （不精确）
 - lruclock update
 - stat_peak_memory 内存峰值
 - trackOperationsPerSecond() 抽样估算
 - shutdown_asap 关闭讯号SIGTERM (kill -15) sigtermHandler()
先 RDB 再关闭
 - databaseCron() 过期处理
 - aof_rewrite_sheduled 执行因为BGSAVE 过程被延迟的 BGREWRITEAOF 
 - rdb_child_pid 与 aof_child_pid 持久化子进程运行状态 （是否开始/wait结束)
 - aof 缓冲区 写入文件
 - cronloops++
