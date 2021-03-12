# binlog
记录形式

可用于[[主从复制]]
## row
行更新
数量多

## statement
语句重新执行一遍
但语义可能 不一致 比如 UUID

## Mixed

### statement 需要转 row 的情况
1. 当 DML 语句更新一个 NDB 表时；
2. 当函数中包含 UUID() 时；
3. 2 个及以上包含 AUTO_INCREMENT 字段的表被更新时；
4. 执行 INSERT DELAYED 语句时；
5. 用 UDF 时；
6. 视图中必须要求运用 row 时，例如建立视图时使用了 UUID() 函数；

## 配置值
```java
log-bin=mysql-bin
#binlog_format=STATEMENT
#binlog_format=ROW
binlog_format=MIXED
```