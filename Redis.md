### 快照持久化
* 一次性全部备份
* dbfilename dump.rdb
* save s k  超过多少秒有多少key被修改久备份
* ./redis-cli -h ip -p 端口 bgsave
### AOF持久化
* appendonly yes
* appendfilename appendonly.aof
* appendfsync always/everysec/no
* bgsave
* bgrewriteaof

### 主从模式
slaveof ip 端口

### 安全
CONFIG get requirepass
CONFIG set requirepass
AUTH [password]

