# 大 key

## what
大value
大容器 上万元素
big key 命令可以查看

## 缺点
分配内存耗时
占用内存高
qps 高
低版本 删除时间长 

读写大key会导致超时严重，甚至阻塞服务。
如果删除大key，DEL命令可能阻塞Redis进程数十秒，使得其他请求阻塞，对应用程序和Redis集群可用性造成严重的影响。
建议每个key不要超过M级别。

## 解决
分页删
hget 部分value
再hash 到 不同 小集合上

memory usage
3.4+
lazy free [[bio]]

 UNLINK 命令替代 D