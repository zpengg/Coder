# Redis 优化
连接池优化 ： 增加core 数量
[[大 key]]
热key
集中过期 [[expire]]

RDBAOF 
cpu： 密集 保证cpu 不与其他应用抢， 其他应用避免绑定cpu
io： 开销大 不与存储服务共用
带宽： 大的rdb同时发送到多个slave
AOF 最耗时的刷盘操作，放到后台线程中也会影响到 Redis 主线程 fsync 阻塞了write 

Redis 机器最好专项专用

最大打开文件句柄

内存大页 fork过程中 会申请 申请会耗时
建议关闭

内存 swap 频繁： 内存不够


碎片整理 4.0+ 老版本要重启

