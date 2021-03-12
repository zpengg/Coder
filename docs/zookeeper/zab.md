# zab
简化版 2PC 

两阶段： 消息广播 + 崩溃恢复
## 消息广播
全部由 Leader 接收，Leader 将请求封装成一个事务 Proposal，
将其发送给所有 Follwer ，然后，
根据所有 Follwer 的反馈，如果**超过半数成功响应**，则执行 commit 操作

### 事务 ZXID 保证顺序
事务，并给这个事务分配一个全局递增的唯一 ID，称为事务ID（ZXID）

Leader epoch  + 事务ID

## 崩溃恢复
解决单点 leader崩溃问题

### 原则
ZAB 协议确保那些已经在 Leader 提交的事务最终会被所有服务器提交。
ZAB 协议确保丢弃那些只在 Leader 提出/复制，但没有提交的事务。

找出 ZXID 最大 的 就可以做 leader
> 可以省去 Leader 服务器检查事务的提交和丢弃工作的这一步操作。

### 数据同步 (重连)
当 Follower 链接上 Leader 之后，Leader 服务器会根据自己服务器上最后被提交的 ZXID 和 Follower 上的 ZXID 进行比对，比对结果要么回滚，要么和 Leader 同步。 

[[选举]]
## 2PC
ZAB

通过崩溃恢复解决了 2PC 的单点问题，
通过队列解决了 2PC 的同步阻塞问题。