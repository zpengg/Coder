# raft
1. 发 我要当leader
2. 接收方 接收消息，leader先到先得,并回复投票的<leader_runid,leader_epoch> 
3. 1 个 epoch 只投一次
4. 获得回复超半数, 即为全局leader
5. 一段时间内election timeout, 回复不够，选举限时，超时leader_epoch++， 重选
election timeout 随机 150ms - 300ms

[RAFT动图](http://thesecretlivesofdata.com/raft/)
