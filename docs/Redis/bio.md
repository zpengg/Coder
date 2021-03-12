# bio (background I/O service)
目前有两种任务：fsync和close。每种任务一个队列和一个线程。

## fsync 
fsync 和 使用和主线程的write存在冲突，分出来不会阻塞 write