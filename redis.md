# redis

## 命令

### 基本语法

* 启动客户端

```shell
reeis-cli
```

* 远程操作

```shell
redis-cli -h host -p port -a password
```

### 键（KEY）操作

| 1    | [DEL key](https://www.w3cschool.cn/redis/keys-del.html) 该命令用于在 key 存在时删除 key。 |
| ---- | ------------------------------------------------------------ |
| 2    | [DUMP key](https://www.w3cschool.cn/redis/keys-dump.html) 序列化给定 key ，并返回被序列化的值。<br /> |
| 3    | [EXISTS key](https://www.w3cschool.cn/redis/keys-exists.html) 检查给定 key 是否存在。 |
| 4    | [EXPIRE key](https://www.w3cschool.cn/redis/keys-expire.html) seconds 为给定 key 设置过期时间。 |
| 5    | [EXPIREAT key timestamp](https://www.w3cschool.cn/redis/keys-expireat.html) EXPIREAT 的作用和 EXPIRE 类似，都用于为 key 设置过期时间。 不同在于 EXPIREAT 命令接受的时间参数是 UNIX 时间戳(unix timestamp)。 |
| 6    | [PEXPIRE key milliseconds](https://www.w3cschool.cn/redis/keys-pexpire.html) 设置 key 的过期时间以毫秒计。 |
| 7    | [PEXPIREAT key milliseconds-timestamp](https://www.w3cschool.cn/redis/keys-pexpireat.html) 设置 key 过期时间的时间戳(unix timestamp) 以毫秒计 |
| 8    | [KEYS pattern](https://www.w3cschool.cn/redis/keys-keys.html) 查找所有符合给定模式( pattern)的 key 。![image-20210914163118289](https://dxytoll-img-1304942391.cos.ap-nanjing.myqcloud.com/img/2021-09-14 16:31:23-image-20210914163118289-da0c9731c1faf831825a5178650d6242.png) |
| 9    | [MOVE key db](https://www.w3cschool.cn/redis/keys-move.html) 将当前数据库的 key 移动到给定的数据库 db 当中。 |
| 10   | [PERSIST key](https://www.w3cschool.cn/redis/keys-persist.html) 移除 key 的过期时间，key 将持久保持。 |
| 11   | [PTTL key](https://www.w3cschool.cn/redis/keys-pttl.html) 以毫秒为单位返回 key 的剩余的过期时间。 |
| 12   | [TTL key](https://www.w3cschool.cn/redis/keys-ttl.html) 以秒为单位，返回给定 key 的剩余生存时间(TTL, time to live)。 |
| 13   | [RANDOMKEY](https://www.w3cschool.cn/redis/keys-randomkey.html) 从当前数据库中随机返回一个 key 。 |
| 14   | [RENAME key newkey](https://www.w3cschool.cn/redis/keys-rename.html) 修改 key 的名称 |
| 15   | [RENAMENX key newkey](https://www.w3cschool.cn/redis/keys-renamenx.html) 仅当 newkey 不存在时，将 key 改名为 newkey 。 |
| 16   | [TYPE key](https://www.w3cschool.cn/redis/keys-type.html) 返回 key 所储存的值的类型。 |

### string

| 1    | [SET key value](https://www.w3cschool.cn/redis/strings-set.html) 设置指定 key 的值 |
| ---- | ------------------------------------------------------------ |
| 2    | [GET key](https://www.w3cschool.cn/redis/strings-get.html) 获取指定 key 的值。 |
| 3    | [GETRANGE key start end](https://www.w3cschool.cn/redis/strings-getrange.html) 返回 key 中字符串值的子字符![image-20210914163206226](https://dxytoll-img-1304942391.cos.ap-nanjing.myqcloud.com/img/2021-09-14 16:32:06-image-20210914163206226-538eb6de1438138d7be8d9c7a97e84e8.png) |
| 4    | [GETSET key value](https://www.w3cschool.cn/redis/strings-getset.html) 将给定 key 的值设为 value ，并返回 key 的旧值(old value)。 |
| 5    | [GETBIT key offset](https://www.w3cschool.cn/redis/strings-getbit.html) 对 key 所储存的字符串值，获取指定偏移量上的位(bit)。 |
| 6    | [MGET key1 [key2..\]](https://www.w3cschool.cn/redis/strings-mget.html) 获取所有(一个或多个)给定 key 的值。 |
| 7    | [SETBIT key offset value](https://www.w3cschool.cn/redis/strings-setbit.html) 对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。 |
| 8    | [SETEX key seconds value](https://www.w3cschool.cn/redis/strings-setex.html) 将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)。 |
| 9    | [SETNX key value](https://www.w3cschool.cn/redis/strings-setnx.html) 只有在 key 不存在时设置 key 的值。 |
| 10   | [SETRANGE key offset value](https://www.w3cschool.cn/redis/strings-setrange.html) 用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始。 |
| 11   | [STRLEN key](https://www.w3cschool.cn/redis/strings-strlen.html) 返回 key 所储存的字符串值的长度。 |
| 12   | [MSET key value [key value ...\]](https://www.w3cschool.cn/redis/strings-mset.html) 同时设置一个或多个 key-value 对。 |
| 13   | [MSETNX key value [key value ...\]](https://www.w3cschool.cn/redis/strings-msetnx.html) 同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。 |
| 14   | [PSETEX key milliseconds value](https://www.w3cschool.cn/redis/strings-psetex.html) 这个命令和 SETEX 命令相似，但它以毫秒为单位设置 key 的生存时间，而不是像 SETEX 命令那样，以秒为单位。 |
| 15   | [INCR key](https://www.w3cschool.cn/redis/strings-incr.html) 将 key 中储存的数字值增一。<br />1. 如果 key 不存在，那么 key 的值会先被初始化为 0 ，然后再执行 INCRBY 命令。<br />2. 如果值包含错误的类型，或字符串类型的值不能表示为数字，那么返回一个错误。<br />3. 本操作的值限制在 64 位(bit)有符号数字表示之内。 |
| 16   | [INCRBY key increment](https://www.w3cschool.cn/redis/strings-incrby.html) 将 key 所储存的值加上给定的增量值（increment） 。<br />1. 如果 key 不存在，那么 key 的值会先被初始化为 0 ，然后再执行 INCRBY 命令。<br />2. 如果值包含错误的类型，或字符串类型的值不能表示为数字，那么返回一个错误。<br />3. 本操作的值限制在 64 位(bit)有符号数字表示之内。 |
| 17   | [INCRBYFLOAT key increment](https://www.w3cschool.cn/redis/strings-incrbyfloat.html) 将 key 所储存的值加上给定的浮点增量值（increment） 。 |
| 18   | [DECR key](https://www.w3cschool.cn/redis/strings-decr.html) 将 key 中储存的数字值减一。 |
| 19   | [DECRBY key decrement](https://www.w3cschool.cn/redis/strings-decrby.html) key 所储存的值减去给定的减量值（decrement） 。 |
| 20   | [APPEND key value](https://www.w3cschool.cn/redis/strings-append.html) 如果 key 已经存在并且是一个字符串， APPEND 命令将 value 追加到 key 原来的值的末尾。 |

### hash

| 1    | [HDEL key field2 [field2\]](https://www.w3cschool.cn/redis/hashes-hdel.html) 删除一个或多个哈希表字段 |
| ---- | ------------------------------------------------------------ |
| 2    | [HEXISTS key field](https://www.w3cschool.cn/redis/hashes-hexists.html) 查看哈希表 key 中，指定的字段是否存在。 |
| 3    | [HGET key field](https://www.w3cschool.cn/redis/hashes-hget.html) 获取存储在哈希表中指定字段的值 |
| 4    | [HGETALL key](https://www.w3cschool.cn/redis/hashes-hgetall.html) 获取在哈希表中指定 key 的所有字段和值 |
| 5    | [HINCRBY key field increment](https://www.w3cschool.cn/redis/hashes-hincrby.html) 为哈希表 key 中的指定字段的整数值加上增量 increment 。 |
| 6    | [HINCRBYFLOAT key field increment](https://www.w3cschool.cn/redis/hashes-hincrbyfloat.html) 为哈希表 key 中的指定字段的浮点数值加上增量 increment 。 |
| 7    | [HKEYS key](https://www.w3cschool.cn/redis/hashes-hkeys.html) 获取所有哈希表中的字段 |
| 8    | [HLEN key](https://www.w3cschool.cn/redis/hashes-hlen.html) 获取哈希表中字段的数量 |
| 9    | [HMGET key field1 (field2)](https://www.w3cschool.cn/redis/hashes-hmget.html) 获取所有给定字段的值 |
| 10   | [HMSET key field1 value1 (field2 value2 )](https://www.w3cschool.cn/redis/hashes-hmset.html) 同时将多个 field-value (域-值)对设置到哈希表 key 中。 |
| 11   | [HSET key field value](https://www.w3cschool.cn/redis/hashes-hset.html) 将哈希表 key 中的字段 field 的值设为 value 。 |
| 12   | [HSETNX key field value](https://www.w3cschool.cn/redis/hashes-hsetnx.html) 只有在字段 field 不存在时，设置哈希表字段的值。 |
| 13   | [HVALS key](https://www.w3cschool.cn/redis/hashes-hvals.html) 获取哈希表中所有值 |
| 14   | HSCAN key cursor [MATCH pattern] [COUNT count] 迭代哈希表中的键值对。 |

### list

| 1    | [BLPOP key1 [key2 \] timeout](https://www.w3cschool.cn/redis/lists-blpop.html) 移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。 |
| ---- | ------------------------------------------------------------ |
| 2    | [BRPOP key1 [key2 \] timeout](https://www.w3cschool.cn/redis/lists-brpop.html) 移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。 |
| 3    | [BRPOPLPUSH source destination timeout](https://www.w3cschool.cn/redis/lists-brpoplpush.html) 从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。 |
| 4    | [LINDEX key index](https://www.w3cschool.cn/redis/lists-lindex.html) 通过索引获取列表中的元素 |
| 5    | [LINSERT key BEFORE\|AFTER pivot value](https://www.w3cschool.cn/redis/lists-linsert.html) 在列表的元素前或者后插入元素 |
| 6    | [LLEN key](https://www.w3cschool.cn/redis/lists-llen.html) 获取列表长度 |
| 7    | [LPOP key](https://www.w3cschool.cn/redis/lists-lpop.html) 移出并获取列表的第一个元素 |
| 8    | [LPUSH key value1 [value2\]](https://www.w3cschool.cn/redis/lists-lpush.html) 将一个或多个值插入到列表头部 |
| 9    | [LPUSHX key value](https://www.w3cschool.cn/redis/lists-lpushx.html) 将一个或多个值插入到已存在的列表头部 |
| 10   | [LRANGE key start stop](https://www.w3cschool.cn/redis/lists-lrange.html) 获取列表指定范围内的元素 |
| 11   | [LREM key count value](https://www.w3cschool.cn/redis/lists-lrem.html) 移除列表元素 |
| 12   | [LSET key index value](https://www.w3cschool.cn/redis/lists-lset.html) 通过索引设置列表元素的值 |
| 13   | [LTRIM key start stop](https://www.w3cschool.cn/redis/lists-ltrim.html) 对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除。 |
| 14   | [RPOP key](https://www.w3cschool.cn/redis/lists-rpop.html) 移除并获取列表最后一个元素 |
| 15   | [RPOPLPUSH source destination](https://www.w3cschool.cn/redis/lists-rpoplpush.html) 移除列表的最后一个元素，并将该元素添加到另一个列表并返回 |
| 16   | [RPUSH key value1 [value2\]](https://www.w3cschool.cn/redis/lists-rpush.html) 在列表中添加一个或多个值 |
| 17   | [RPUSHX key value](https://www.w3cschool.cn/redis/lists-rpushx.html) 为已存在的列表添加值 |

### set

| 1    | [SADD key member1 [member2\]](https://www.w3cschool.cn/redis/sets-sadd.html) 向集合添加一个或多个成员 |
| ---- | ------------------------------------------------------------ |
| 2    | [SCARD key](https://www.w3cschool.cn/redis/sets-scard.html) 获取集合的成员数 |
| 3    | [SDIFF key1 [key2\]](https://www.w3cschool.cn/redis/sets-sdiff.html) 返回给定所有集合的差集 |
| 4    | [SDIFFSTORE destination key1 [key2\]](https://www.w3cschool.cn/redis/sets-sdiffstore.html) 返回给定所有集合的差集并存储在 destination 中 |
| 5    | [SINTER key1 [key2\]](https://www.w3cschool.cn/redis/sets-sinter.html) 返回给定所有集合的交集 |
| 6    | [SINTERSTORE destination key1 [key2\]](https://www.w3cschool.cn/redis/sets-sinterstore.html) 返回给定所有集合的交集并存储在 destination 中 |
| 7    | [SISMEMBER key member](https://www.w3cschool.cn/redis/sets-sismember.html) 判断 member 元素是否是集合 key 的成员 |
| 8    | [SMEMBERS key](https://www.w3cschool.cn/redis/sets-smembers.html) 返回集合中的所有成员 |
| 9    | [SMOVE source destination member](https://www.w3cschool.cn/redis/sets-smove.html) 将 member 元素从 source 集合移动到 destination 集合 |
| 10   | [SPOP key](https://www.w3cschool.cn/redis/sets-spop.html) 移除并返回集合中的一个随机元素 |
| 11   | [SRANDMEMBER key [count\]](https://www.w3cschool.cn/redis/sets-srandmember.html) 返回集合中一个或多个随机数 |
| 12   | [SREM key member1 [member2\]](https://www.w3cschool.cn/redis/sets-srem.html) 移除集合中一个或多个成员 |
| 13   | [SUNION key1 [key2\]](https://www.w3cschool.cn/redis/sets-sunion.html) 返回所有给定集合的并集 |
| 14   | [SUNIONSTORE destination key1 [key2\]](https://www.w3cschool.cn/redis/sets-sunionstore.html) 所有给定集合的并集存储在 destination 集合中 |
| 15   | [SSCAN key cursor [MATCH pattern\] [COUNT count]](https://www.w3cschool.cn/redis/sets-sscan.html) 迭代集合中的元素 |

### zset

| 1    | [ZADD key score1 member1 [score2 member2\]](https://www.w3cschool.cn/redis/sorted-sets-zadd.html) 向有序集合添加一个或多个成员，或者更新已存在成员的分数 |
| ---- | ------------------------------------------------------------ |
| 2    | [ZCARD key](https://www.w3cschool.cn/redis/sorted-sets-zcard.html) 获取有序集合的成员数 |
| 3    | [ZCOUNT key min max](https://www.w3cschool.cn/redis/sorted-sets-zcount.html) 计算在有序集合中指定区间分数的成员数 |
| 4    | [ZINCRBY key increment member](https://www.w3cschool.cn/redis/sorted-sets-zincrby.html) 有序集合中对指定成员的分数加上增量 increment |
| 5    | [ZINTERSTORE destination numkeys key [key ...\]](https://www.w3cschool.cn/redis/sorted-sets-zinterstore.html) 计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中 |
| 6    | [ZLEXCOUNT key min max](https://www.w3cschool.cn/redis/sorted-sets-zlexcount.html) 在有序集合中计算指定字典区间内成员数量 |
| 7    | [ZRANGE key start stop [WITHSCORES\]](https://www.w3cschool.cn/redis/sorted-sets-zrange.html) 通过索引区间返回有序集合成指定区间内的成员 |
| 8    | [ZRANGEBYLEX key min max [LIMIT offset count\]](https://www.w3cschool.cn/redis/sorted-sets-zrangebylex.html) 通过字典区间返回有序集合的成员 |
| 9    | [ZRANGEBYSCORE key min max [WITHSCORES\] [LIMIT]](https://www.w3cschool.cn/redis/sorted-sets-zrangebyscore.html) 通过分数返回有序集合指定区间内的成员 |
| 10   | [ZRANK key member](https://www.w3cschool.cn/redis/sorted-sets-zrank.html) 返回有序集合中指定成员的索引 |
| 11   | [ZREM key member [member ...\]](https://www.w3cschool.cn/redis/sorted-sets-zrem.html) 移除有序集合中的一个或多个成员 |
| 12   | [ZREMRANGEBYLEX key min max](https://www.w3cschool.cn/redis/sorted-sets-zremrangebylex.html) 移除有序集合中给定的字典区间的所有成员 |
| 13   | [ZREMRANGEBYRANK key start stop](https://www.w3cschool.cn/redis/sorted-sets-zremrangebyrank.html) 移除有序集合中给定的排名区间的所有成员 |
| 14   | [ZREMRANGEBYSCORE key min max](https://www.w3cschool.cn/redis/sorted-sets-zremrangebyscore.html) 移除有序集合中给定的分数区间的所有成员 |
| 15   | [ZREVRANGE key start stop [WITHSCORES\]](https://www.w3cschool.cn/redis/sorted-sets-zrevrange.html) 返回有序集中指定区间内的成员，通过索引，分数从高到底 |
| 16   | [ZREVRANGEBYSCORE key max min [WITHSCORES\]](https://www.w3cschool.cn/redis/sorted-sets-zrevrangebyscore.html) 返回有序集中指定分数区间内的成员，分数从高到低排序 |
| 17   | [ZREVRANK key member](https://www.w3cschool.cn/redis/sorted-sets-zrevrank.html) 返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序 |
| 18   | [ZSCORE key member](https://www.w3cschool.cn/redis/sorted-sets-zscore.html) 返回有序集中，成员的分数值 |
| 19   | [ZUNIONSTORE destination numkeys key [key ...\]](https://www.w3cschool.cn/redis/sorted-sets-zunionstore.html) 计算给定的一个或多个有序集的并集，并存储在新的 key 中 |
| 20   | [ZSCAN key cursor [MATCH pattern\] [COUNT count]](https://www.w3cschool.cn/redis/sorted-sets-zscan.html) 迭代有序集合中的元素（包括元素成员和元素分值） |