# CPU
CPU 抢占
CPU 绑定

## CPU 缓存
CPU 缓存可以分为一级缓存，二级缓存，部分高端 CPU 还具有三级缓存

[缓存未命中消耗时间数据](https://images2015.cnblogs.com/blog/897247/201608/897247-20160823201305464-895015043.png)

### 缓存行 64Byte 为单位

## 跨核
跨核访问需要内存控制器参与
可让核主动发送给另外一个核

## 缓存行状态 MESI协议
现在主流的处理器都是用它来保证缓存的相干性和内存的相干性，[[MESI]]

## false sharing
[[FalseSharing]]