https://lixuekai.blog.csdn.net/article/details/108640630
集群角色
Leader、Follower、Observer

ZNode的类型
持久节点：一旦创建，除非主动移除，否则会一直保存在ZooKeeper。

临时节点：生命周期和客户端会话绑定，会话失效，相关的临时节点被移除。

持久顺序性：同时具备顺序性。

临时顺序性：同时具备顺序性。

权限
CREATE
READ
WRITE
DELETE
ADMIN

[[zab]]