# Redis使用手册


Redis 使用手册笔记。

<!--more-->

---

参考：

- [Redis 使用手册](https://book.douban.com/subject/34836750/)
- [Redis 官方文档](https://redis.io/docs/)

<br/>
<br/>

# 前言

关于 Redis 的基本信息，比如它提供了什么功能，它能做什么，它的优点是什么。

<br/>
<br/>

## 简介

Redis 是一个开源的内存数据数据结构存储器，经常用作数据库、缓存以及消息代理等。

Redis 的几个特点：

- 结构丰富：字符串、散列、列表、集合、有序集合、位图、流、地理坐标等一系列丰富的数据结构。用户还可以通过事务、Lua 脚本、模块等特性，扩展已有数据结构的功能。
- 功能完备：自动过期、流水线、事务、数据持久化等。单机和多机使用。
- 速度飞快：它将所有数据存储在内存中。
- 用户友好：Redis API 遵循 UNIX 一次只做一件事，并把它做好的设计哲学。
- 支持广泛

<br/>
<br/>

## 执行命令

Redis 为每种数据结构和功能特性都提供了相应的命令，掌握如何使用这些命令很重要。

Redis 的 `PING` 命令用于测试客户端和服务器之间的连接是否正常。

```sh
redis-cli PING
PONG
```

<br/>
<br/>

## 配置服务器

两种方式：

- 通过命令行和参数来启动
- 通过配置文件来启动

<br/>

---

<br/>

# 字符串

字符串（string）是最基本的键值对类型。

键和值既可以是普通的文本数据，也可以是二进制数据（图片、视频、音频和压缩文件等）。

<br/>
<br/>

## 为字符串键设置值

```redis
# 为字符串键设置值
# O(1)
SET key value

# 默认情况下，对一个已经存在的 key 设置值将导致旧的值被覆盖。
# v2.6.12 可选 NX（不覆盖） 和 XX（只会在有值的情况下执行覆盖） 选项来决定是否覆盖
SET key value NX
SET key value XX
```

<br/>
<br/>

## 获取字符串键的值

```redis
# 获取字符串键的值
# O(1)
GET key
```

<br/>
<br/>

## 获取旧值并设置新值

```redis
# 获取旧值并设置新值
# O(1)
GETSET key new_value
```

<br/>
<br/>

## 一次为多个字符串键设置值

执行多条 SET 命令需要客户端和服务器之间进行多次网络通信，并因此消耗大量的时间。而使用一条 MSET 命令只需要一次网络通信，从而有效地减少程序执行多个设置操作的时间。

```redis
# 一次为多个字符串键设置值
# O(N)
MSET k1 v1 [k2 v2 ...]
```

<br/>
<br/>

## 一次获取多个字符串键的值

MGET 命令也可以将执行多次获取操作所需的网络通信次数从原来的 N 次降低为一次，从而有效地提高程序的运行效率。

```redis
# 一次获取多个字符串键的值
# O(N)
MGET k1 [k2 ...]
```

<br/>
<br/>

## 只在键不存在时为多个字符串键设置值

MSETNX 只会在给定键都不存在的情况下对键进行设置，而不会像 MSET 那样覆盖键的值。

注意，是给定键都不存在。

```redis
# 只有在键不存在的情况下，一次为多个字符串键设置值
# O(N)
MSETNX k1 v1 [k2 v2 ...]
```

<br/>
<br/>

## 获取字符串值的字节长度

```redis
# 获取字符串值的字节长度
# O(1)
STRLEN key
```

<br/>
<br/>

## 字符串值的索引

因为每个字符串都是由一系列连续的字节组成，所以字符串中的每个字节实际上都拥有与之相对应的索引。通过索引对某个部分进行处理。

![字符串的索引示例](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/2-5.png)

<br/>
<br/>

## 获取字符串值指定索引范围上的内容

```redis
# 获取字符串值指定索引范围上的内容
# O(N)
GETRANGE key start end

GETRANGE hello 0 4
"hello"
GETRANGE message -11 -7
"hello"
```

<br/>
<br/>

## 对字符串值的指定索引范围进行设置

```redis
# 对字符串值的指定索引返回进行设置
SETRANGE key index substitute

GET message
"hello world"

SETRANGE message 6 "Redis"
GET message
"hello Redis"
```

<br/>
<br/>

## 自动扩展被修改的字符串

当用户给定的新内容比被替换的内容更长时，SETRANGE 命令就会自动扩展被修改的字符串值，从而确保可以顺利写入。

```redis
GET message
"hello Redis"

SETRANGE message 5 ", this a messge."

GET message
"hello, this is a message."
```

<br/>
<br/>

## 在值里面填充空字节

SETRANGE 命令除了会根据用户给定的系内容自动扩展字符串值之外，还会根据用户给定的 index 索引扩展字符串。

当用户给定的 index 超出字符串值的长度时，字符串值末尾知道索引 `index-1` 之间的部分将使用空字节进行填充。

```redis
# 长度为 5
GET greeting
"hello"

# 长度变为 15
SETRANGE greeting 10 "world"

GET greeting
"hello\x00\x00\x00\x00\x00world"
```

<br/>
<br/>

## 追加新内容到值的末尾

```redis
# 追加新内容到值的末尾
APPEND key suffix

# 如果键不存在，APPEND 命令会先将键的值出师未空字符串""，然后再执行追加操作，相当于 SET。
```

<br/>
<br/>

## 使用字符串键存储数字值

如果值为一下两种类型，那么 Redis 就会把这个值当作数字来处理。

第一种类型是能够使用 C 语言的 long long int 类型存储的整数，在大多数系统中，此类型存储 64 位长度的有符号整数。

第二种类型是能够使用 C 语言的 long double 类型存储的浮点数，在大多数系统中，此类型存储 128 位长度的有符号浮点数。

<br/>
<br/>

## 对整数值执行加减法

当字符串键的值不能被 Redis 解释为整数时，对键执行整数的加减操作将返回错误。

当加减操作遇到不存在的键时，命令会先将键的值初始化为 0，然后再执行相应的加减操作。

```redis
# 对整数值执行加减法操作
INCRBY key increment
DECRBY key increment

SET number 100
INCRBY number 300
DECRBY number 200
```

<br/>
<br/>

## 对整数值执行自增或自减

对整数执行自增或自减的场景经常出现。

```redis
# O(1)

# 加 1
INCR key

# 减 1
DECR key
```

<br/>
<br/>

## 对数字值浮点数执行加减操作

INCRBYFLOAT 遇到不存在的键时，会先将键的值初始化为 0，然后再执行相应的加减操作。

此命令既可用于浮点数值，也可用于整数值。增量既可以是整数值，也可以是浮点数值。

```redis
# O(1)
INCRBYFLOAT key increment

SET decimal 3.14
INCRBYFLOAT decimal 2.55
INCRBYFLOAT decimal -1.1

SET pi 1
INCRBYFLOAT pi 2.14
INCRBYFLOAT pi -0.14
```

<br/>
<br/>

## 小数位长度限制

虽然 Redis 并不限制字符串键存储的浮点数的小数位长度，但是使用 INCRBYFLOAD 命令处理浮点数时，命令最多保留计算结果小数点后的 17 位数字。

<br/>

---

<br/>

# 散列

Redis 的散列会将一个键和一个散列在数据库里关联起来，用户可以在散列中为任意多个字段设置值。

散列包含的字段在实际中是以无序方式进行排序的。

<br/>
<br/>

## 为字段设置值

如果字段已经存在于散列中，那么这次设置就是一次更新操作。

```redis
# 为字段设置值
# O(1)
HSET key field value

HSET hash01 title "greeting"
HSET hash01 author "peter"
HSET  hash01 content "hello"

# 更新字段值
HSET hash01 content "hello world"
```

<br/>
<br/>

## 只在字段不存在的情况下为它设置值

```redis
# 只在字段不存在的情况下才为它设置值，不会覆盖
# O(1)
HSETNX key field value

HSETNX hash01 author "zhang"
0 -- 设置失败，因为字段已存在

HSETNX hash01 views 100
1
```

<br/>
<br/>

## 获取字段的值

获取不存在的字段，将返回空值。

```redis
# 获取字段的值
# O(1)
HGET key field

HGET hash01 title
```

<br/>
<br/>

## 对字段存储的整数执行加减操作

对非整数执行会报错。

```redis
# 对字段值整数进行加减操作
# O(1)
HINCRBY key field increment

HINCRY hash01 views 3
HINCRY hash01 views -2
```

<br/>
<br/>

## 对字段存储的浮点数执行加减法操作

HINCRBYFLOAT 命令可以操作整数。

```redis
# 对字段值浮点数执行加减法操作
# O(1)
HINCRBYFLOAT key field increment
```

<br/>
<br/>

## 获取字段值的字节长度

```redis
# 获取字段值的字节长度
# O(1)
HSTRLEN key field

HSTRLEN hash01 title
```

<br/>
<br/>

## 获取散列包含的字段数量

```redis
# 获取散列包含的字段数量
# O(1)
HLEN key
```

<br/>
<br/>

## 检查字段是否存在

```redis
# 检查字段是否存在于散列中
# O(1)
HEXISTS key field
```

<br/>
<br/>

## 删除字段

如果散列或字段不存在，将返回 0 表示失败。

```redis
# 删除散列中的字段及其值
# O(1)
HDEL key field
```

<br/>
<br/>

## 一次为多个字段设置值

HMSET 命令会覆盖已有的字段值。

```redis
# 一次为多个字段设置值
# O(N)
HMSET key f1 v1 [f2 v2 ...]
```

<br/>
<br/>

## 一次获取多个字段的值

```redis
# 一次获取多个字段的值
# O(N)
HMGET key f1 [f2 ...]
```

<br/>
<br/>

## 获取所有字段和值

Redis 散列包含的字段在底层是以无序方式存储的。如果又需要，用户可以对这些命令返回的元素进行排序，使它们从无序变为有序。

```redis
# O(N)

# 获取所有字段
HKEYS key

# 获取所有值
KVALS key

# 获取所有字段和值
HGETALL key
```

<br/>

---

<br/>

# 列表

Redis 的列表是一种线性的有序结构，可以按照元素被推入列表中的顺序来存储元素，并且列表中的元素可以重复出现。

<br/>
<br/>

## 将元素推入列表左端或右端

如果列表不存在，将自动创建一个空列表，并将元素推入刚刚创建的列表中。

```redis
# 将一个或多个元素推入给定列表的左端
# O(N)
LPUSH key item [item ...]

LPUSH todo "watch tv"
LPUSH todo "finish homework"
LPUSH anothor-todo "to do 01" "to do 02"

# 在列表右端推入一个或多个元素
# O(N)
RPUSH key item [item ...]
```

<br/>
<br/>

## 只对已存在的列表执行推入操作

如果列表不存在，则会放弃推入操作。

每次只能推入单个元素，给定多个元素将报错。

```redis
# O(1)
LPUSHX key item
RPUSHX key item
```

<br/>
<br/>

## 弹出列表最左端或右端的元素

如果列表不存在，将返回一个空值，表示列表为空。

```redis
# O(1)
# 弹出列表最左端的元素
LPOP key

# 弹出列表最右端的元素
RPOP key
```

<br/>
<br/>

## 将右端弹出的元素推入左端

如果源列表不存在，此命令将放弃操作，并返回空值表示执行失败。

如果源列表非空，但目标列表为空，那么将正常执行（也就是新建了一个目标空列表，并推入元素）。

```redis
# 将右端弹出的元素推入左端
# O(1)
RPOPLPUSH source target

RPOPLPUSH key1 key2
# 源和目标相同，表示把列表最右端的元素变为最左端的元素
RPOPLPUSH key1 key1
```

<br/>
<br/>

## 获取指定索引上的元素

列表的每个元素都有与之对应的正数索引和负数索引。

如果给定的索引超出范围，那么将返回空值，以此来表示并不存在的元素。

```redis
# 获取指定索引上的单个元素
# O(N)
LINDEX key index

LINDEX todo 0
LINDEX todo -1
```

<br/>
<br/>

## 获取指定索引范围上的元素

如果起始索引和结束索引都超出了范围，那么将返回空。

如果只有其中一个索引超出了范围，命令将对索引进行修改，获取实际的元素。

```redis
# O(N)
LRANGE key start end

LRANGE todo 0 2
LRANGE todo -3 -1
# 获取所有元素
LRANGE todo 0 -1
```

<br/>
<br/>

## 为指定索引设置新元素

如果超出索引范围，将返回错误。

```redis
# O(N)
LSET key index new_element

LSET todo 0 "play video games"
```

<br/>
<br/>

## 将元素插入列表

将一个元素插入到列表的某个元素之前/后。

如果目标元素不存在，那么操作会失败。

```redis
# O(N)
LINSERT key BEFORE|AFTER target_element new_element

LINSERT todo AFTER "play video games" "sleep"
```

<br/>
<br/>

## 保留或移除列表中的指定元素

LTRIM 命令，移除列表中给定索引范围之外的所有元素，只保留给定范围内的元素。

LREM 命令，从列表中移除指定元素。

```redis
# O(N)
LTRIM key start end

LTRIM todo 0 2


# count 等于0，移除包含所有指定元素。
# count 大于0，从左向右检查，并移除最先发现的 count 数量的指定元素。
# count 小于0，从右向左检查，并移除最先发现的 abs(count) 数量的指定元素。
LREM key count element

RPUSH testl1 "a" "b" "b" "a" "c" "a"
RPUSH testl2 "a" "b" "b" "a" "c" "a"
RPUSH testl3 "a" "b" "b" "a" "c" "a"

# 移除所有 a 元素，移除后列表："b" "b" "c"
LREM testl1 0 "a"

# 移除最左端两个 a 元素，移除后列表："b" "b" "c" "a"
LREM testl2 2 "a"

# 移除最右端两个 a 元素，移除后列表："a" "b" "b" "c"
LREM testl3 -2 "a"
```

<br/>
<br/>

## 阻塞式左端或右端弹出操作

BLPOP 命令，从左往右依次检查列表，对最先遇到的非空列表执行左端元素弹出操作。如果检查了所有列表后都没有发现可以弹出的非空列表，那么它将阻塞执行该命令的客户端并开始等待，知道某个给定列表变为非空，又或者等待时间超时。

BRPOP 命令。从右往左。

如果在客户端被阻塞的过程中，有另一个客户端向导致阻塞的列表推入了新的元素，那么该列表就会变为非空，而被阻塞的客户端也会随着命令成功弹出列表元素而重新回到非阻塞状态。

如果所有列表在给定时限内都是空列表，那么命令将在给定时限到达后向客户端返回一个空值，表示没有任何元素被弹出。

阻塞效果只会对执行这个命令的客户端有效，其他客户端以及 Redis 服务器不受影响。

```redis
# O(N)
# 超时时间单位为秒

# 阻塞式左端弹出操作
BLPOP key [key ...] timeout

BLPOP todo 5


# 阻塞式右端弹出操作
BRPOP key [key ...] timeout

BRPOP todo 5
```

<br/>
<br/>

## 阻塞式弹出并推入操作

如果源列表为空，命令将阻塞执行该命令的客户端，然后在给定的时限内等待可弹出的元素出现，或者超时。

```redis
# O(1)
BRPOPLPUSH source target timeout

BRPOPLPUSH list1 list2 5
```

<br/>

---

<br/>

# 集合

Redis 的集合（set）允许用户将任意多个各不相同的元素存储到集合中。集合是无序的，且元素不可重复。

列表元素可以重复，集合元素不可重复。列表以有序方式存储元素，集合以无序方式存储元素。这样就导致了两者命令的复杂度不同。

对于集合来说，所有针对单个元素的集合命令都不需要遍历整个集合，所以复杂度都为 O(1)。

<br/>
<br/>

## 将元素添加到集合

向集合中添加重复元素会被忽略。

```redis
# 将一个或多个元素添加到集合
# O(N)
SADD key element [element ...]

SADD databases "Redis" "MySQL" "MongoDB"
```

<br/>
<br/>

## 从集合中移除元素

不存在的元素会被忽略。

```redis
# 从集合中移除一个或多个元素
# O(N)
SREM key element [element ...]

SREM databases "Redis"
```

<br/>
<br/>

## 将元素从一个集合移动到另一个集合

元素不存在于源集合，命令返回 0（表示失败）。
元素存在于目标集合，命令将会覆盖目标集合的相同元素。

```redis
# 将指定元素从源集合移动到目标集合
# O(1)
SMOVE source target element
```

<br/>
<br/>

## 获取集合包含的所有元素

因为集合元素是无序的，所以结果顺序可能会有不同。

```redis
# 获取集合包含的所有元素
# O(N)
SMEMBERS key
```

<br/>
<br/>

## 获取集合包含的元素数量

获取给定集合的大小（元素数量）。

```redis
# O(1)
SCARD key
```

<br/>
<br/>

## 检查给定元素是否存在于集合

```redis
# 检查元素是否存在于集合中
# O(1)
SISMEMBER key element

SISMEMBER databases "Redis"
```

<br/>
<br/>

## 随机获取集合中的元素

如果 count 为正，将返回 count 个不重复的元素。如果大于集合数量，则返回所有元素。如果为负数，将返回 abs(count) 个可能有重复的数。

```redis
# 从集合中随机地获取指定数量的元素
# O(N)
SRANDMEMBER key [count]

SRANDMEMBER databases
SRANDMEMBER databases -2
```

<br/>
<br/>

## 随机地从集合中移除指定数量的元素

```redis
# 从集合中随机移除指定数量的元素
# O(N)
SPOP key [count]
```

<br/>
<br/>

## 对集合进行交集计算

```redis
# 计算给定集合的交集，返回交集中的元素
# O(N*M)
SINTER key [key ...]

SINTER s1 s2

# 把交集结果存储到指定的键里，如果键已经存在，此命令会先删除存在的键
# O(N*M)
SINTERSTORE destination_key key [key ...]

SINTERSTORE s1_inter_s2 s1 s2
```

<br/>
<br/>

## 对集合进行并集计算

```redis
# 计算出所有集合的并集，然后返回并集的所有元素
# O(N)
SUNION key [key ...]

# 把并集结果存储到指定的键，它会覆盖已有的键
# O(N)
SUNIONSTORE destination_key key [key ...]

SUNION s1 s2
SUNIONSTORE s1_union_s2 s1 s2
```

<br/>
<br/>

## 对集合进行差集计算

命令会按照给定集合的顺序，从左到右以此对集合执行差集计算。

```redis
# 计算给定集合之间的差集，并返回差集包含的所有元素
# O(N)
SDIFF key [key ...]

# 把差集存储到指定的键
# O(N)
SDIFFSTORE destionation_key key [key ...]

# 先计算 s1-s2 的差集，得到的结果和 s3 计算差集
SDIFF s1 s2 s3

SDIFFSTORE diff-result s1 s2 s3
```

<br/>
<br/>

## 执行集合计算的注意事项

对集合执行交集、并集、差集等计算需要耗费大量的资源，所以应该尽量使用 `SINTERSTORE` 等命令来存储并重用计算结果，而不要每次都重复进行计算。

<br/>

---

<br/>

# 有序集合

Redis 的有序集合（sorted set）同时具有有序和集合两种性质。这种数据结构中的每个元素都由一个成员和一个与成员相关的分值组成，成员以字符串方式存储，分值以 64 位双精度浮点数格式存储。

有序集合中的每个成员也都是独一无二的，不会出现重复的成员。但不同成员的分值可以相同。分值相同时，将按照成员在字典序中的大小对其进行排列。

有序集合的分值除了数字之外，还可以是 `+inf` 或 `-inf`，表示无穷大和无穷小。

![有序集合示例](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/6-1.png)

<br/>
<br/>

## 添加或更新成员

此命令除了向有序集合添加成员之外，还可以对有序集合中已存在的成员的分值进行更新。

```redis
# 向有序集合中添加一个或多个新成员
# O(M*log(N))，M 为给定成员的数量，N 为有序集合包含的成员数量。
ZADD key score member [score member]

ZADD salary 3500 "A" 4000 "B" 3000 "C"
ZADD salary 3500 "C"

# 通过 XX（只更新） 或 NX（只添加） 选项
ZADD key [XX|NX] score member [score member ...]

# 默认情况下，ZADD 命令返回新添加成员的数量作为返回值。从 3.2 版本开始，可使用 CH 选项，让命令返回被修改成员的数量。

ZADD key [CH] score member [score member ...]
```

<br/>
<br/>

## 移除指定的成员

如果成员不存在于有序集合中，命令将自动忽略该成员。

```redis
# 从有序集合中移除一个或多个成员和它们的分值
# O(M*log(N))，M 为给定成员的数量，N 为有序集合包含的成员数量。
ZREM key member [member ...]

ZREM salary "C"
```

<br/>
<br/>

## 获取成员的分值

```redis
# 获取给定成员相关联的分值
# O(1)
ZSCORE key member
```

<br/>
<br/>

## 对成员的分值执行自增或自减操作

如果成员并不存在于有序集合，或有序集合不存在，命令将直接把给定成员添加到有序集合中，并把增量设置为该成员的分值。

```redis
# 分值自增或自减
# O(log(N))
ZINCRBY key increment member

ZINCRBY salary 500 "A"
ZINCRBY salary -500 "A"

ZINCRBY salary 4500 "D"
ZINCRY salarys 4500 "D"
```

<br/>
<br/>

## 获取有序集合的大小

如果有序集合不存在，将返回 0。

```redis
# 获取有序集合包含的成员数量
# O(1)
ZCARD key
```

<br/>
<br/>

## 获取成员在有序集合中的排名

如果有序集合不存在，将返回一个空值。

```redis
# 按升序排列排名
# O(log(N))
ZRANK key member

# 按降序排列排名
# O(log(N))
ZREVRANK key member
```

<br/>
<br/>

## 获取指定索引范围内的成员

如果有序集合不存在，将返回一个空列表。

```redis
# 通过索引获取，按分值升序排列
# O(log(N) + M)，N 为有序集合包含的成员数，M 为命令返回的成员数
ZRANGE key start end

# 通过索引获取，按分值降序排列
ZREVRANGE key start end

ZRANGE salary 0 3
ZRANGE salary -3 -1

# 默认只返回成员，可通过 WITHSCORES 选项也获取分值
ZRANGE|ZREVRANGE key start end [WITHSOCRES]
```

<br/>
<br/>

## 获取指定分值范围内的成员

```redis
# min/max 指定最小分值和最大分值
# O(log(N) + M)，N 为有序集合包含的成员数，M 为返回的成员数
ZRANGEBYSCORE key min max
ZREVRANGEBYSCORE key max min

ZRANGEBYSCORE salary 3000 5000
ZREVRANGEBYSCORE salary 5000 3000

# 命令默认返回成员，可通过 WITHSCORES 选项来同时获取成员及其分值
ZRANGEBYSCORE key min max [WITHSCORES]
ZREVRANGEBYSCORE key max min [WITHSCORES]

# 命令默认返回所有成员，可通过 LIMIT 选项限制返回的成员数量， offset 参数指示在返回结果前需要跳过的成员数量，count 参数指示命令最多可以返回多少个成员
ZRANGEBYSCORE key min max [LIMIT offset count]
ZREVRANGEBYSCORE key max min [LIMIT offset count]

ZRANGEBYSCORE salary 3000 5000 LIMIT 1 2

# 命令默认是闭区间，也就是给定分值的最大和最小也会包含在内。可在分值参数前使用单括号(转为开区间，这样就不包含
ZRANGEBYSCORE salary (3000 5000 WITHSCORES

# 使用无限值作为范围，+inf 和 -inf
ZRANGEBYSOCRE salary -inf (5000 WITHSCORES
```

<br/>
<br/>

## 统计指定分值范围内的成员数量

```redis
# 统计有序集合中分值介于指定范围内的成员数量
# O(log(N))
ZCOUNT key min max

ZCOUNT salary 3000 5000
```

<br/>
<br/>

## 移除指定排名范围内的成员

```redis
# 从升序排列的有序集合中移除位于指定排名范围内的成员
# O(log(N) + M)，N 为有序集合包含的成员数量，M 为被移除的成员数量。
ZREMRANGEBYRANK key start end

ZREMRANGEBYRANK salary 0 2
```

<br/>
<br/>

## 移除指定分值范围内的成员

```redis
# 从有序集合中移除位于指定分值范围内的成员，并在移除操作执行完毕返回被移除成员的数量
# O(log(N) + M)，N 为有序集合包含的成员数量，M 为被移除的成员数量。
ZREMRANGEBYSCORE key min max

ZREMRANGEBYSCORE salary 3000 4000
```

<br/>
<br/>

## 有序集合的并集运算和交集运算

```redis
# 有序集合的并集运算
ZUNIONSTORE destination numbers key [key ...]

# 有序集合的交集运算
ZINTERSTORE destination numbers key [key ...]

ZUNIONSTORE union-result-1 2 sorted_set1 sorted_set2
ZINTERSTORE inter-result-1 2 sorted_set1 sorted_set2
```
















<br/>

---

<br/>

# HyperLogLog

HyperLogLog 是一个专门为了计算集合的基数而创建的概率算法，对于一个给定的集合，HyperLogLog 可计算出这个集合的近似基数。















<br/>

---

<br/>

# 位图

<br/>

---

<br/>

# 地理坐标

<br/>

---

<br/>

# 流

<br/>

---

<br/>

# 数据库

Redis 为数据库提供了非常丰富的操作命令，通过这些命令，用户可以：

- 指定自己想要使用的数据库。
- 一次性获取数据库包含的所有键，迭代地获取数据库包含的所有键，或随机地获取数据库中的某个键。
- 根据给定键的值进行排序。
- 检查给定的一个或多个键，看它们是否存在于数据库当中。
- 查看给定键的类型。
- 对给定键进行重命名。
- 移除指定的键，或将它从一个数据库移动到另一个数据库。
- 清空数据库所包含的所有键。
- 交换给定的两个数据库。

<br/>
<br/>

## 切换到指定的数据库

默认清空下，Redis 在启动时会创建 16（0-15） 个数据库。在不同的库中使用相同的键是可以的。

当用户使用客户端与 Redis 服务器进行连接时，一般默认都会使用 0 号数据库。

```redis
# 切换库
# 复杂度 O(1)
SELECT <db>
```

<br/>
<br/>

## 获取所有与给定匹配符相匹配的键

`KEYS` 命令接受一个全局匹配符作为参数，返回当前库中所有相匹配的键。

```redis
# 获取所有键，不建议这么用
# 当键数量比较大时，可能会导致服务器被阻塞
KEYS *

# 获取特定前缀的键
KEYS test*
KEYS user::*
```

<br/>

`KEYS` 命令允许使用多种不同的全局匹配符作为 pattern 参数，下表是一些常见的全局匹配符。

复杂度 `O(N)`，N 为库包含的键数量。

| 匹配符 | 作用 | 示例 |
| - | - | - |
| `*` | 匹配零个或多个任意字符 | `user::*` 匹配前缀 <br> `*z`匹配后缀 <br> `*x:x*` 匹配中间 |
| `?` | 匹配任意的单个字符 | `user::i?` 匹配 user::id |
| `[]` | 匹配给定字符串中的单个字符 | `user::i[abc]` 匹配 user::ia |
| `[开始-结束]` | 匹配给定范围的单个字符 | `user::i[a-d]` 匹配 user::id |

<br/>
<br/>

## 以渐进方式迭代数据库中的键

因为 `KEYS` 命令需要检查库所包含的所有键，并一次性将符合条件的所有键全部返回给客户端。所以当库包含的键的数量比较大时，可能会导致服务器被阻塞。

为了解决这个问题，Redis 从 2.8.0 版本开始提供了 `SCAN` 命令，该命令是一个迭代器，它每次调用的时候都会从库中获取一部分键，用户可以通过重复调用此命令来迭代库包含的所有键。

```redis
SCAN <cursor>
```

结果由两个元素组成：

- 第一个元素是进行下一次迭代所需的游标。如果为 0，说明客户端已经对库完成了以此完整的迭代。
- 第二个元素是一个列表，包含了本次迭代取得的库的键。如果没有获取到任何键，则是一个空列表。
  - 可能会返回重复的键。去重需要自己在客户端中进行检测和过滤。
  - 返回的键的数量是不确定的，有时甚至会不返回任何键，但只要返回的游标不为 0，迭代就没有结束。

<br/>
<br/>

### 以此简单的迭代示例

以 0 作为游标的示例：

```redis
# 这个调用告知我们下次迭代应该使用 25 作为游标，并返回了11 个键的键名。
redis> SCAN 0
1) "25" -- 进行下次迭代的游标
2) 1) "key::16" -- 本次迭代获取到的键
   2) "key::2"
   3) "key::6"
   4) "key::8"
   5) "key::13"
   6) "key::22"
   7) "key::10"
   8) "key::24"
   9) "key::23"
   10) "key::21"
   11) "key::5"

redis> SCAN 25
1) "31"
2) 1) "key::20"
   2) "key::18"
   3) "key::19"
   4) "key::7"
   5) "key::1"
   6) "key::9"
   7) "key::12"
   8) "key::11"
   9) "key::17"
   10) "key::15"
   11) "key::14"
   12) "key::3"

redis> SCAN 31
1) "0"
2) 1) "key::0"
   2) "key::4"
```

<br/>
<br/>

### 迭代命令的迭代保证

针对数据库的一次完整迭代以用户给定游标 0 调用 `SCAN` 命令开始，直到返回游标 0 结束。迭代命令提供了以下保证：

- 从迭代开始到结束，一直存在于库中的键总会被返回。
- 如果一个键在迭代的过程中被添加到库中，那么这个键是否会被返回是不确定的。
- 如果一个键在迭代的过程中被移除了，那么此命令在它被移除之后将不再返回这个键，但这个键在被移除之前仍有可能被此命令返回。
- 无论库如何变化，迭代总是有始有终的，不会出现循环迭代或其他无法终止的情况。

<br/>
<br/>

### 游标的使用

在很多数据库中，使用游标都要显式地申请，并在迭代完成之后释放游标，否则就会造成内存泄漏。

于此相反，`SCAN` 命令的游标不需要申请，也不需要释放，它们不占用任何资源，每个客户端都可以使用自己的游标独立地对库进行迭代。

此外，用户可以随时在迭代的过程中停止迭代，或随时开始一次新的迭代，这不会浪费任何资源，也不会引发任何问题。

<br/>
<br/>

### 迭代与给定匹配符相匹配的键

默认情况下，`SCAN` 命令会返回所有键（也就是 `KEYS *` 命令调用的一个迭代版本）。但是通过 `MATCH` 选项可以指定匹配的键。

```redis
SCAN <cursor> [MATCH pattern]

redis> SCAN 0 MATCH user::*
```

<br/>
<br/>

### 指定返回键的期望数量

一般情况下，`SCAN` 命令返回的键数量是不确定的，但可以通过 `COUNT` 选项提供一个期望值，以此来说明希望得到多少键。

需要注意，期望的键数量并不意味着精确的键数量。

在用户没有显式地指定 `COUNT` 的情况下，`SCAN` 命令将使用 10 作为默认值。

```redis
SCAN <cursor> [COUNT number]
```

<br/>
<br/>

### 数据结构迭代命令

与获取数据库键的 `KEYS` 命令一样，Redis 的各个数据结构也存在一些可能导致服务器阻塞的命令：

- 散列的 `HKEYS`, `HVALS` 和 `HGETALL` 命令在处理包含键值对较多的散列时，可能会导致服务器阻塞。
- 集合的 `SMEMBERS` 命令在处理包含元素较多的集合时，可能会导致服务器阻塞。
- 有序集合的一些范围型获取命令（如 `ZRANGE`），也有阻塞服务器的可能。

为了解决上述情况，Redis 为散列、集合和有序集合也提供了与 `SCAN` 命令类似的游标迭代命令。分别是：`HSCAN`、`SSCAN` 和 `ZSCAN` 命令。

```redis
# 散列迭代命令
# 当散列包含较多键值对的时候，应该尽量使用HSCAN代替HKEYS、HVALS和HGETALL，以免造成服务器阻塞。
HSCAN <hash> <cursor> [MATCH] [COUNT]

# 渐进式集合迭代命令
# 当集合包含较多元素的时候，我们应该尽量使用SSCAN代替 SMEMBERS，以免造成服务器阻塞。
SSCAN set cursor [MATCH pattern] [COUNT number]

# 渐进式有序集合迭代命令
# 当有序集合包含较多成员的时候，我们应该尽量使用ZSCAN去代替 ZRANGE以及其他可能会返回大量成员的范围型获取命令，以免造成服务器阻塞。
ZSCAN sorted_set cursor [MATCH pattern] [COUNT number]
```

<br/>
<br/>

## 随机返回一个键

`RANDOMKEY` 命令从数据库中随机返回一个键，当库为空时，将返回空值。

```redis
# 复杂度 O(1)
RANDOMKEY
```

<br/>
<br/>

## 对键的值进行排序

通过执行 `SORT` 命令对列表元素、集合元素或有序集合成员进行排序。

```redis
# 对指定键存储的元素执行数字值排序
SORT <key>

# 指定排序方式，默认升序
[ASC|DESC]
SORT lucky-numbers DESC

# 字符串值排序，默认是数字排序
[ALPHA]
SORT fruits ALPHA

# 只获取部分排序结果，offset 从 0 开始计算。
[LIMIT offset count]
SORT fruits ALPHA LIMIT 2 1

# 获取外部键的值作为结果
SORT key [[GET pattern] [GET pattern] ...]
SORT fruits ALPHA GET *-price
SORT fruits ALPHA GET *-info->inventory

# 使用外部键的值作为排序权重
SORT key [BY pattern]
SORT fruits BY *-price

# 保存排序结果
SORT key [STORE destination]
SORT fruits ALPHA STORE sorted-fruits
```

<br/>
<br/>

## 检查给定键是否存在

使用 `EXISTS` 命令，检查一个或多个键是否存在于当前库中。从 3.0.3 版本开始支持多个键，之前版本只支持单个键。

```redis
EXISTS key [key ...]
EXISTS k1 key2
```

<br/>
<br/>

## 获取数据库包含的键值对数量

使用 `DBSIZE` 命令获取当前库包含了多少个键值对。

```redis
# O(1)
DBSIZE
```

<br/>
<br/>

## 查看键的类型

`TYPE` 命令允许查看给定键的类型。

```redis
# 复杂度 O(1)
TYPE key
```

不同类型的键的返回结果

| 键类型 | 返回值 |
| - | - |
| 字符串值 | string |
| 散列键 | hash |
| 列表键 | list |
| 集合键 | set |
| 有序集合键 | zset |
| HyperLogLog | string |
| 位图 | string |
| 地理位置 | zset |
| 流 | stream |

<br/>
<br/>

## 修改键名

使用命令修改键的名称。

```redis
# 复杂度 O(1)

# RENAME 会移除存在的新名称的键
RENAME origin new

# 只在新键名尚未被占用的情况下进行改名
RENAMENX origin new
```

如果指定的新键名已经被占用，那么 `RENAME` 命令会先移除占用了新键名的那个键，然后再执行改名操作。

但 `RENAMENX` 命令只会在新键名尚未被占用的情况下进行改名，如果用户指定的新键名已经被占用，那么命令将放弃执行改名操作。

<br/>
<br/>

## 将一个键移动到另一个库

将一个键从当前库移动至目标库。

如果给定键不存在于当前库，或目标库存在同名的键，那么命令将不做动作，只返回 0 表示移动失败。

```redis
# 复杂度 O(1)
MOVE key db
```

<br/>
<br/>

## 移除指定的键

从当前正在使用的库中移除一个或多个键，以及这些键相关联的值。

这个命令隐含着一个性能问题：`DEL` 命令会以同步方式执行移除操作，所以如果待移除的键非常庞大或数量众多，那么服务器可能会被阻塞。

比如，移除一个包含上百万个元素的集合，移除一个包含数十万键值对的散列，或一次移除成千上万个键，都可能引起服务器阻塞。

```redis
# O(N), N 为被移除键的数量
DEL key [key ...]
```

<br/>
<br/>

## 以异步的方式移除指定的键

为了解决上面的 `DEL` 命令的性能问题，Redis 从 4.0 版本开始新添加了一个 `UNLINK` 命令。

此命令只会在库中移除对改键的引用（reference），而对键的实际移除操作则会交给后台线程执行，因此不会造成服务器阻塞。

```redis
# O(N)
UNLINK key [key ...]
```

<br/>
<br/>

## 清空当前数据库

`FLUSHDB` 命令会遍历用户正在使用的库，移除其中包含的所有键值对。

由于 `FLUSHDB` 命令也是一个同步移除命令，并且因为移除的是整个库而不是单个键，所以它常常会引发比 `DEL` 命令更为严重的服务器阻塞现象。

为了解决这个问题，Redis 4.0 给此命令新增了一个 `async` 选项。此选项将操作放在后台线程中以异步方式进行，这样就不会阻塞服务器了。

```redis
# O(N), N 为被清空库包含的键值对数量。
FLUSHDB

FLUSHDB async
```

建议对这个命令增加别名，以避免误删除库。

```conf
# redis.conf
rename-command FLUSHDB ABCDEFG
```

<br/>
<br/>

## 清空所有数据库

`FLUSHALL` 命令可以清空 Redis 服务器包含的所有库。

与 `FLUSHDB` 命令一样，以同步方式执行也可能会导致服务器阻塞，因此 Redis 4.0 同样添加了 `async` 选项。

```redis
# O(N), N 为被清空库包含的键值对数量。
FLUSHALL

FLUSHALL async
```

建议对这个命令增加别名，以避免误删库。

```conf
# redis.conf
rename-command FLUSHALL HIJKLMN
```

<br/>
<br/>

## 互换数据库

从 Redis 4.0 版本开始，`SWAPDB` 命令接受两个库号码，然后对指定的两个库进行互换。

因为互换数据库这一操作可以通过调整指向数据库的指针来实现，这个过程不需要移动库中的任何键值对，所以复杂度为 `O(1)`，并且此命令不会导致服务器阻塞。

```redis
SWAPDB x y
```

<br/>

---

<br/>

# 自动过期

Redis 提供了自动的键过期功能（key expiring）。通过此功能，用户可以让特定的键在指定的时间之后自动被移除。

<br/>
<br/>

## 设置和更新键的生存时间

通过 `EXPIRE`（秒级） 或 `PEXPIRE`（毫秒级） 命令为键设置一个生存时间（TTL）。

```redis
# O(1)
# Redis 1.0 版本可用
EXPIRE key seconds

# Redis 2.6 版本可用
PEXPIRE key milliseconds
```

<br/>

当对一个已经带有生存时间的键再次执行 `EXPIRE` 或 `PEXPIRE` 命令时，键原有的生存时间将会被移除，并设置新的生存时间。

<br/>
<br/>

## SET命令的EX选项和PX选项

在使用键过期功能时，组合使用 `SET` 命令和 `EXPIRE/PEXPIRE` 命令的做法是非常常见的。

Redis 从 2.6.12 版本为 `SET` 命令提供了 `EX` 选项和 `PX` 选项，这和执行执行那两个命令的效果是一样的。

```redis
# O(1)
SET key value [EX seconds] [PX milliseconds]

```

<br/>

使用带有 EX 选项或 PX 选项的 `SET` 命令除了可以减少命令的调用数量并提升程序的执行速度之外，更重要的是保证了操作的原子性，使得为键设置值和为键设置生存时间这两个操作可以一起执行。

设置缓存和设置生存时间这两个操作，要么一起成功，要么一起失败，实现是安全的。

<br/>
<br/>

## 设置键的过期时间

`EXPIREAT` 命令（秒级精度的 UNIX 时间）和 `PEXPIREAT` 命令（毫秒级精度的 UNIX 时间），通过设置过期时间（expire time），让 Redis 在指定 UNIX 时间来临之后自动移除给定的键。

同样，再次执行命令来更新键的过期时间。

```redis
# O(1)

# 从 Redis 1.2.0 版本可用
EXPIREAT key seconds_timestamp

# 从 Redis 2.6.0 版本可用
PEXPIREAT key milliseconds_timestamp
```

<br/>
<br/>

## 获取键的剩余生存时间

使用 `TTL`（秒为单位） 或 `PTTL`（毫秒为单位） 命令查看键的剩余生存时间。

如果给定的键没有设置生存时间和过期时间，那么将返回 -1。如果给定的键不存在，将返回 -2。

在使用 TTL 命令时，有时会遇到命令返回 0 的情况。出现这种情况的原因在于它的精度是秒级，所以当给定键的剩余生存时间不足 1s 时，只能返回 0 作为结果。

```redis
# O(1)
TTL key

PTTL key
```

<br/>

---

<br/>

# 流水线和事务

在执行命令的时候，我们总是单独地执行每个命令，也就是说，先讲一个命令发送到服务器，等服务器执行完这个命令并将结果返回给客户端之后，再执行下一个命令，一次类推，直到所有命令都执行完为止。

这种执行命令的方式虽然可行，但在性能方面却不是最优的，并且在执行时可能还会出现一些非常隐蔽的错误。为了解决这些问题，Redis 使用了流水线特性和事务特性，前者可以有效提升  Redis 的性能，后者则可以避免单独执行命令时可能会出现的一些错误。

<br/>
<br/>

## 流水线

一般情况下，用户每执行一个 Redis 命令，Redis 客户端和 Redis 服务器就需要执行以下步骤：

1. 客户端向服务器发送命令请求
2. 服务器接收命令请求，并执行用户指定的命令调用，然后产生相应的命令执行结果
3. 服务器向客户端返回命令的执行结果
4. 客户端接收命令的执行结果，并向用户进行展示

与大多数网络程序一样，执行 Redis 命令所消耗的大部分时间都用在了发送命令请求和接收命令结果上：Redis 处理一个请求通常只需要很短的时间，但客户端将命令发送给服务器以及服务器向客户端返回命令结果的过程却需要花费不少时间。通常情况下，程序需要执行的 Redis 命令越多，它需要进行网络通信操作也会越多，程序的执行速度也会因此变慢。

为了解决此问题，可以使用 Redis 提供的流水线特性。这个特性允许客户端把任意多条 Redis 命令请求打包在一起，然后一次性将它们全部发送给服务器。而服务器则会在流水线包含的所有命令请求都处理完毕之后，一次性将它们的执行结果全部返回给客户端。

通过使用流水线特性，可以将执行多个命令所需的网络通信次数从原来的 N 次降低为 1 次，这可以大幅地减少程序在网络通信方面耗费的时间，使得程序的执行效率得到显著的提升。

虽然 Redis 服务器提供了流水线特性，但还需要客户端支持才能使用。

虽然 Redis 服务器并不会限制客户端在流水线中包含的命令数量，但是却会为客户端的输入缓冲区设置默认值为 1GB 的体积上限。但客户端发送的数据量超过这一限制时，Redis 服务器将强制关闭该客户端。

<br/>
<br/>

## 事务

虽然 Redis 允许一次向列表推入多个元素，但是列表每次只能弹出一个元素。Redis 并没有提供能够一次弹出多个列表元素的命令。

为了实现一个正确且安全的弹出多个元素的目的，我们需要一种能够让服务器将多个命令打包起来一并执行的技术——这正是事务的特性。

- 事务可以将多个命令打包成一个命令来执行，当事务成功执行时，事务中包含的所有命令都会被执行。
- 相反，如果事务没有成功执行，那么它包含的所有命令都不会被执行。
- 因此，事务是原子的。通过使用事务，用户可以保证自己想要执行的多个命令要么全部被执行，要么一个都不执行。

<br/>
<br/>

### 开启事务

通过执行 `MULTI` 命令来开启一个新的事务。

进入事务模式后，用户输入的所有命令不会立即执行，而是按顺序放入一个事务队列中，等待事务执行时再统一执行。

```redis
# O(1)
# 从 Redis 1.2.0 版本可用
MULTI
```

示例：

```redis
redis> MULTI
OK

redis> SET title "Hand in Hand"
QUEUED

redis> SADD fruits "apple" "banana" "cherry"
QUEUED

redis> RPUSH numbers 123 456 789
QUEUED
```

<br/>
<br/>

### 执行事务

通过执行 `EXEC` 命令来执行事务。

当事务成功执行时，将返回一个列表作为结果，这个列表会按照命令的入队顺序一次执行包含各个命令的执行结果。

```redis
EXEC
```

例子：

```redis
# 事务包含的所有命令的复杂度之和
# 从 Redis 1.2.0 版本可用
redis> MULTI -- 1) 开启事务
OK

redis> SET title "Hand in Hand" -- 2) 命令入队
QUEUED

redis> SADD fruits "apple" "banana" "cherry"
QUEUED

redis> RPUSH numbers 123 456 789
QUEUED

redis> EXEC -- 3)执行事务
1) OK -- SET命令的执行结果
2) (integer) 3 -- SADD命令的执行结果
3) (integer) 3 -- RPUSH命令的执行结果
```

<br/>
<br/>

### 放弃事务

执行 `DISCARD` 命令放弃已开启的事务。

此命令会清空事务队列中已有的所有命令，并让客户端退出事务模式。

```redis
# O(N) N 为十五队列包含的命令数量
# 从 Redis 2.0.0 版本可用
DISCARD
```

示例：

```redis
redis> MULTI
OK

redis> SET page_counter 10086
QUEUED

redis> SET download_counter 12345
QUEUED

redis> DISCARD
OK
```

<br/>
<br/>

### 事务的安全性

在对数据库的事务特性进行介绍时，人们一般会对数据库对 ACID 性质的支持程序去判断数据库的事务是否安全。

具体来说，Redis 的事务总是具有 ACID 中的 ACI 属性：

- 原子性（Atomic）：如果事务成功，那么事务中所包含的所有命令都会执行；反之，都不会执行。
- 一致性（Consistent）：Redis 服务器会对事务及其包含的命令进行检查，确保无论事务是否成功执行，事务本身都不会对数据库造成破坏。
- 隔离性（Isolate）：每个 Redis 客户端都拥有自己独立的事务队列，并且每个 Redis 事务都是独立执行的，不同事务之间不会互相干扰。
- 耐久性（Durable）：当事务执行完毕后，它的结果将被存储在硬盘中。

<br/>
<br/>

### 事务对服务器的影响

因为事务在执行时会独占服务器，所以用户应该避免在事务中执行过多命令，更不要将一些需要进行大量计算的命令放入事务中，以免造成服务器阻塞。

<br/>
<br/>

### 流水线和事务的组合

流水线与事务虽然在概念上相似，但是在作用上却并不相同。流水线的作用是将多个命令打包，然后一并发送至服务器；而事务的作用是将多个命令打包，然后让服务器一并执行它们。

因为 Redis 的事务在 `EXEC` 命令前并不会产生实际效果，所以很多 Redis 客户端都会使用流水线去包裹事务命令，并将入队的命令缓存在本地，等到用户输入 `EXEC` 命令之后，再将所有事务通过流水线一并发送到服务器，这样客户端在执行事务时就可以达到“打包发送，打包执行”的最优效果。

<br/>
<br/>

## 带有乐观锁的事务

我们需要一种机制，它可以保证如果锁键的值在 XXX 命令之后发生了变化，那么 YYY 命令将不会被执行。在 Redis 中，这种机制被称为乐观锁。

<br/>
<br/>

### 对键进行监视

通过执行 `WATCH` 命令，要求服务器对一个或多个键进行监视。如果在客户端尝试执行事务之前，这些键的值发生了变化，那么服务器将拒绝执行客户端发送的事务，并向它返回一个空值。

于此相反，如果所有被监视的键都没有发生任何变化，那么服务器将会如常地执行客户端发送的事务。

```redis
# O(N) N 为被监视键的数量
# 从 Redis 2.2.0 版本可用
WATCH key [key ...]
```

通过同时使用 `WATCH` 命令和事务，可以构建出一种针对被监视键的乐观锁机制，确保事务只会在被监视键没有发生任何变化的情况下执行，从而保证了事务对被监视键的所有修改都是安全、正确和有效的。

一个因为乐观锁机制而导致事务执行失败的示例：

```redis
redis> WATCH user_id_counter
OK

redis> GET user_id_counter -- 获取当前最新的用户ID
"256"

redis> MULTI
OK

redis> SET user::256::email "peter@spamer.com" -- 尝试使用这个ID来存储用户信息
QUEUED

redis> SET user::256::password "topsecret"
QUEUED

redis> INCR user_id_counter -- 创建新的用户ID
QUEUED

redis> EXEC
(nil) -- user_id_counter键已被修改
```

<br/>
<br/>

### 取消对键的监视

通过执行 `UNWATCH` 命令，取消对键的监视。

除了显式地执行 `UNWATCH` 命令之外，使用 `EXEC` 命令执行事务和使用 `DISCARD` 命令取消事务，同样会导致客户端撤销对所有键的监视，因为这两个命令在执行后都会隐式地调用 `UNWATCH` 命令。

```redis
# O(N) N 为被取消监视的键数量
# 从 Redis 2.2.0 版本可用
UNWATCH
```

示例：

```redis
redis> WATCH "lock_key" "user_id_counter" "msg"
OK

redis> UNWATCH -- 取消对以上3个键的监视
OK
```

<br/>

---

<br/>

# Lua脚本

从 Redis 2.6.0 版本开始支持 Lua 脚本，它可以让用户对 Redis 服务器内置的 Lua 解释器中执行指定的 Lua 脚本。被执行的 Lua 脚本可以直接调用 Redis 命令，并使用 Lua 语言及其内置的函数库处理命令结果。

Lua 脚本特性的出现给 Redis 带来了很大的变化，其中最重要就是使得用户可以按需对 Redis 服务器的功能进行扩展。在此之前，只能通过修改源代码来扩展新功能。

Lua 脚本带来的第二个变化与它的执行机制有关：Redis 服务器以原子方式执行 Lua 脚本，在执行完整个 Lua 脚本及其包含的 Redis 命令之前，Redis 服务器不会执行其他客户端发送的命令或脚本，因此被执行的 Lua 脚本天生就具有原子性。

Lua 脚本的另一个好处是它能够在保证原子性的同时，一次在脚本中执行多个 Redis 命令，可以有效地提升升序的执行效率。虽然流水线加上事务也可以，但 Lua 脚本缓存特性能够更为有效地减少带宽占用。

Redis 在 Lua 环境中内置了一些非常有用的包，通过这些包，用户可以直接在服务器端对数据进行处理，然后把它们存储到数据库中，这可以有效地减少不必要的网络传输。

举个例子。用户可以通过 Lua 环境内置的 cjson 包，在服务器端直接将执行命令所得的结果序列化为 JSON 数据，并将其存储到数据库中。这比“先将命令执行结果返回给客户端，接着由客户端将结果序列化为 JSON，最后再通过写入命令将序列化后的 JSON 数据存储到数据库中”的做法更简单方便。

本章内容需要了解一定的 Lua 语言知识。

<br/>
<br/>

## 执行Lua脚本

使用 `EVAL` 命令来执行给定的 Lua 脚本。

```redis
EVAL
```



































<br/>

---

<br/>

# 持久化

Redis 把所有数据都存在内存中，而传统数据库通常只会把数据的索引存储在内存中，并将实际的数据存储在硬盘中。

但内存数据断电后就会丢失。为了解决此问题，Redis 提供了数据持久化功能。此功能把内存中存储的数据以文件形式存储到硬盘上，而服务器根据这些文件在系统停机之后实施数据恢复，让服务器重新回到停机之前的状态。

为了满足不同的持久化需求，Redis 提供了 RDB 持久化、AOF 持久化和 RDB-AOF 混合持久化等方式。当然用户也可以关闭持久化功能。

<br/>
<br/>

## RDB持久化

RDB持久化是 Redis 默认使用的持久化功能，它创建出一个经过压缩过的二进制文件（文件以 `.rdb` 结尾，表示 Redis DataBase），其中包含了服务器在各个数据库中存储的键值对数据等信息。

<br/>
<br/>

### 阻塞服务器并创建RDB文件

执行 `SAVE` 命令，要求 Redis 服务器以同步方式创建一个记录了服务器当前所有数据库的 RDB 文件。

```redis
# O(N) N 为 Redis 服务器所有数据库包含的键值对总数量
# 从 Redis 1.0.0 版本可用
redis> SAVE
OK
```

接收到 `SAVE` 命令的 Redis 服务器将遍历数据库包含的所有库，并将各个库包含的键值对全部记录到 RDB 文件中。在 SAVE 命令执行期间，Redis 服务器将阻塞，直到 RDB 文件创建完毕为止。如果已经存在 RDB 文件，那么服务器将使用新文件替换旧文件。

<br/>
<br/>

### 非阻塞方式创建RDB文件

因为 `SAVE` 命令在执行期间会阻塞服务器，在此期间服务器无法为其他客户端提供服务。为了解决这个问题，Redis 提供了 `BGSAVE` 异步命令。BGSAVE 命令不会直接使用 Redis 服务器进程创建 RDB 文件，而是使用子进程创建 RDB 文件。

当 Redis 服务器接收到 `BGSAVE` 命令，将执行以下操作：

- 创建一个子进程。
- 子进程执行 BGSAVE 命令，创建新的 RDB 文件。
- 文件创建完毕后，子进程退出并通知 Redis 服务器进程。
- Redis 服务器使用新文件替换已有旧文件。

```redis
# O(N) N 为 Redis 服务器所有数据库包含的键值对总数量
# 从 Redis 1.0.0 版本可用
redis> BGSAVE
Background saving started
```

因为 BGSAVE 是异步执行，所以 Redis 服务器在 BGSAVE 命令执行期间仍可以继续处理其他客户端的请求。需要注意，虽然 BGSAVE 命令不会像 SAVE 命令一样一直阻塞 Redis 服务器，但由于需要创建子进程，所以父进程占用的内存数量越大，创建子进程这一操作耗费的时间也会越长。因此 BGSAVE 命令仍可能会由于创建子进程而短暂地阻塞服务器。

<br/>
<br/>

### 通过配置项自动创建RDB文件

通过设置 save 配置项，让 Redis 服务器在满足指定条件时自动执行 BGSAVE 命令。

```conf
# seconds 指定触发持久化操作所需的时长
# changes 指定触发持久化操作所需的修改次数
# 如服务器在 seconds 秒内，对其包含的各个库总共执行了至少 changes 次修改，那么服务器将自动执行一次 BGSAVE 命令。
save <seconds> <changes>
```

可以同时配置多个 save 选项。

```conf
# 默认配置
save 60 10000
save 300 100
save 3600 1
```

注意，为了避免同时使用多个触发条件而导致服务器过于频繁地执行 BGSAVE 命令，Redis 服务器在每次成功创建 RDB 文件后，负责自动触发 BGSAVE 命令的时间计数器以及修改次数计数器都会被清理并重新开始计数。

<br/>
<br/>

### RDB文件结构

RDB 文件的总体结构顺序：

- RDB 文件标识符
- 版本号
- 设备附加信息
- 数据库数据
- Lua 脚本缓存
- EOF
- CRC64 校验和

<br/>

RDB 文件标识符：标识符内容为 "REDIS" 这五个字符。Redis 服务器在尝试载入 RDB 文件的时候，可以通过此标识符快速地判断该文件是否为真正的 RDB 文件。

版本号：是一串字符串格式的数字。新版 Redis 服务器总是能够向下兼容旧版 Redis 服务器生成的 RDB 文件。

设备附加信息：记录了生成 RDB 文件的 Redis 服务器及其所在平台的信息。比如服务器的版本号、宿主机架构、创建文件的时间戳、服务器占用的内存数量等。

数据库数据：记录了 Redis 服务器存储的多个库的数据（按库号码从小到大进行排序）。

Lua 脚本缓存：保存所有已被缓存的 Lua 脚本。

EOF：用于标识 RDB 正文内容的结尾，它的实际值为二进制 `0xFF`。当 Redis 服务器读取到 EOF 时，它直到 RDB 文件的正文部分已经读取完毕。

CRC64 校验和：Redis 服务器在读入 RDB 文件时会通过这个校验和快速地检查 RDB 文件是否出错或者损坏的情况出现。

<br/>
<br/>

### 载入RDB文件

首先，Redis 启动时，它会在工作目录中查找是否有 RDB 文件出现，如果有就打开它，然后读取文件的内容并执行以下载入操作：

1. 检查文件开头的标识符是否为 Redis，不是则报错并终止载入。
2. 检查文件的 RDB 版本号，以此来判断 Redis 服务器能否读取这一个版本的文件。
3. 根据文件中记录的设备附加信息，执行相应的操作和设置。
4. 检查文件数据库数据是否为空，不为空就执行以下子操作。<br>
  4.1 根据文件记录的库号码，切换至正确的库。<br>
  4.2 根据文件记录的键值对总数量以及带有过期时间的键值对数量，设置数据库底层数据结构。<br>
  4.3 一个接一个地载入文件记录的所有键值对数据，并在数据库中重建这些键值对。<br>
5. 如果服务器启用了复制功能，那么将之前缓存的 Lua 脚本重载到缓存中。
6. 遇到 EOF 标识，确认 RDB 文件已全部读取完毕。
7. 载入文件末尾的校验和，把它与载入数据期间计算出的校验和进行对比，以此来判断被载入的数据是否完好无损。
8. 文件载入完毕，服务器开始接受客户端请求。

![RDB 文件的数据载入流程](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/5-7.png)

<br/>
<br/>

### 数据丢失

RDB 文件记录的是服务器在开始创建文件那一刻，服务器中包含的键值对数据，这种数据通常被称为时间点快照（point-in-time snapshot）。时间点快照的一个特点是，系统在停机时将丢失最后一次成功实施持久化时间点之后的所有数据。对于一个只使用 RDB 持久化的 Redis 服务器来说，服务器停机时丢失的数据量取决于最后一次成功执行的时间点快照。

<br/>
<br/>

## AOF持久化

AOF 提供的是增量式的持久化，它的核心原理在于：服务器每次执行完写命令之后，都会以协议文本的方式将被执行的命令追加到 AOF 文件的末尾。这样，服务器在停机之后，只要重新执行 AOF 文件中保存的 Redis 命令，就可以将数据库恢复至停机之前的状态。

在实际的 AOF 文件中，命令都是以 Redis 网络协议的方式保存的。

<br/>
<br/>

### 启用AOF持久化功能

通过 `appendonly` 选项来启用，打开后，Redis 服务器在默认情况下将创建一个名为 `appendonly.aof` 的文件。

```conf
appendonly yes
```

<br/>
<br/>

### 设置AOF文件的冲洗频率

为了提高程序的写入性能，现代操作系统通常会把针对硬盘的多次写操作优化为一次写操作。具体做法是，当程序调用 `WRITE` 系统调用对文件进行写入时，系统并不会直接把数据写入硬盘，而是会先将数据写入位于内存的缓冲区，等到指定的时限到达或满足某些写入条件时，系统才会执行 `FLUSH` 系统调用，将缓冲区的数据冲洗到硬盘。

这种机制虽然提高了程序的性能，但也给程序的写入操作带来了不确定性，特别是对于 AOF 这样持久化功能来说，AOF 文件的冲洗机制将直接影响 AOF 持久化的安全性。

为了消除上述机制带来的不确定性，Redis 提供了 `appendfsync` 选项，来控制系统冲洗 AOF 文件的频率。

```conf
appendfsync everysec

```

此配置项有三个值：

- `always`：没执行一个命令，就对 AOF 文件执行一次冲洗操作。对安全性的追求牺牲了服务器的性能。
- `everysec`：每 1s，就对 AOF 文件执行一次冲洗。默认值。
- `no`：不主动对 AOF 文件执行冲洗操作，有操作系统决定。给可能丢失的数据量带来了不确定性。

除非有明确需求，否则用户不应该随意修改此配置的值。

<br/>
<br/>

### AOF重写

随着服务器不断运行，被执行的命令将变得越来越多，而负责记录这些命令的 AOF 文件也会变得越来越大。与此同时，如果服务器曾经对相同的键执行过多次修改操作，那么 AOF 文件中还会出现多个冗余命令。

示例，冗余的文件命令：

```redis
SELECT 0
SET msg "hello world!"
SET msg "good morning!"
SET msg "happy birthday!"
SADD fruits "apple"
SADD fruits "banana"
SADD fruits "cherry"
SADD fruits "dragon fruit"
SREM fruits "dragon fruit"
SADD fruits "durian"
RPUSH job-queue 10086
RPUSH job-queue 12345
RPUSH job-queue 256512
```

文件中命令对三个键进行了多次修改，但最终修改效果可以简化为以下四条命令：

```redis
SELECT 0
SET msg"happy birthday！"
SADD fruits"apple""banana""cherry""durian"
RPUSH job-queue 1008612345256512
```

<br/>

冗余命令的存在不仅增加了 AOF 文件的体积，并且因为 Redis 服务器在停机之后需要通过重新执行 AOF 文件中保存的命令还恢复数据，所以 AOF 文件中冗余的命令越多，恢复数据耗费的时间也越多。

为了减少冗余命令，让 AOF 文件保持苗条，并提供数据恢复操作的执行速度，Redis 提供了 AOF 重写功能，该功能能够生成一个全新的 AOF 文件，并且文件中只包含恢复当前数据库所需的尽可能少的命令。

通过执行 `BGREWRITEAOF` 命令或设置重写操作配置项来触发 AOF 重写操作。

<br/>

通过显式执行 `BGREWRITEAOF` 命令触发 AOF 重写操作。它是一个异步命令，Redis 服务器在收到该命令之后会创建一个子进程，由它扫描整个数据库并生成新的 AOF 文件。

```redis
# O(N) N 为 Redis 服务器所有数据库包含的键值对总数量。
# 从 Redis 1.0.0 版本可用。
BGREWRITEAOF
Background append only file rewriting started
```

有两点需要注意：

- 如果用户发送 BGREWRITEAOF 命令时，服务器正在创建 RDB 文件，那么服务器将把 AOF 重写操作延后到 RDB 文件创建完毕之后再植行，从而避免两个写硬盘操作同时执行导致机器性能下降。
- 如果服务器再执行重写操作的过程中，又收到了新的 BGREWRITEAOF 命令请求，那么服务器将为新的命令返回重写操作正在进行中的错误信息。

<br/>

通过配置重写选项，让 Redis 自动触发重写操作。

```conf
# 设置自动 AOF 文件重写所需的最小 AOF 文件体积，默认值 64mb。也就是说，小于 64MB 将不出自动执行重写。
auto-aof-rewrite-min-size 64mb
# 设置自动 AOF 文件重写所需的文件体积增大比例，默认值 100。如果当前 AOF 文件的体积比最后一次 AOF 文件重写之后的体积增大了 100%，那么将自动执行重写。
auto-aof-rewrite-percentage 100
```

<br/>
<br/>

### AOF持久化的优缺点

AOF 持久化的缺点：

- AOF 文件是协议文本，所以体积要大的多，并且生成时间更长。
- AOF 持久化需要执行文件中保存的命令来恢复数据库，所以恢复速度慢得多。
- AOF 重写都需要创建子进程，所以在数据库体积较大的情况下，进行 AOF 文件重写将占用大量资源，并导致服务器被短暂地阻塞。

<br/>
<br/>

## RDB-AOF混合持久化

RDB 持久化和 AOF 持久化都有各自的优缺点：

- RDB 持久化可以生成紧凑的 RDB 文件，并且使用 RDB 文件进行数据恢复的速度也非常快，但 RDB 的全量持久化可能会让服务器在停机时丢失大量数据。
- AOF 持久化可以将丢失数据的时间窗口限制在 1s 内，但 AOF 文件会比 RDB 文件大得多，并且数据恢复过程也会相对较慢。

从 Redis 4.0 版本开始引入了 RDB-AOF 混合持久化模式，此模式基于 AOF 构建而来。如果用户启动了 AOF 持久化，并配置 `aof-use-rdb-preamble` 配置项。那么 Redis 服务器再执行 AOF 重写操作时，就会像执行 BGSAVE 命令那样，根据数据库当前的状态生成出相应的 RDB 数据，并将这些数据写入新建的 AOF 文件中。至于那些在 AOF 重写开始之后执行的 Redis 命令，则会继续以协议文本的方式追加到新 AOF 文件的末尾，及已有的 RDB 数据的后面。

换句话说，在开启了 RDB-AOF 混合持久化功能之后，服务器生成的 AOF 文件将由两部分组成，其中位于 AOF 文件开头的是 RDB 格式的数据， 而跟在 RDB 数据后面的则是 AOF 格式的数据。

```conf
aof-use-rdb-preamble yes
```

Redis 目前默认是没有打开 RDB-AOF 混合持久化功能的。但 Redis 作者声称，RDB-AOF 混合持久化模式将在未来取代传统的 RDB 持久化。

<br/>
<br/>

## 同时使用RDB持久化和AOF持久化

在 RDB-AOF 混合持久化功能出现之前，不少追求安全性的使用者会同时使用 RDB 持久化和 AOF 持久化。但有了 RDB-AOF 之后，同时使用两种持久化已经不再必要。

同时使用这两种持久化，需要注意以下问题：

- 同时使用两种持久化功能需要消耗大量系统资源，请确保硬件足够。
- Redis 服务器在启动时，会优先使用 AOF 文件进行恢复数据。只有在没有检测到 AOF 文件时，才会考虑寻找并使用 RDB 文件进行数据恢复。
- 如果在生成 RDB 文件时发送 BGREWRITEAOF 命令，重写操作将推迟到 RDB 文件创建后再执行，避免两种持久化同时执行并抢占系统资源。
- 同样，如果正在执行 BGWRITEAOF 命令时执行 BGSAVE，则命令也会推迟到重写操作之后再执行。

总的来说，在数据持久化问题上，Redis 4.0 之后版本的使用者应该优先使用 RDB-AOF 混合持久化。而之前的 Redis 版本中，如果用户不知道自己应该使用哪种持久化，那么可以优先选用 AOF 持久化，并将 RDB 持久化作为辅助的数据备份手段。

<br/>
<br/>

## 无持久化

即便用户没有显式地开启 RDB 持久化和 AOF 持久化，Redis 服务器也会使用默认的 RDB 持久化配置。

```conf
save 60 10000
save 300 100
save 3600 1
```

如果用户想要彻底关闭持久化，需要修改 save 配置项。

```conf
# 禁用持久化
save ""
```

<br/>
<br/>

## 关闭Redis服务器

通过执行 `SHUTDOWN` 命令来关闭 Redis 服务器。

默认情况下，当 Redis 服务器收到 SHUTDOWN 命令时，它将执行以下动作：

- 停止处理客户端发送的命令请求。
- 根据服务器的持久化配置，决定是否执行数据保存操作。
- 服务器进程退出。

```redis
# O(N) N 为需要持久化的键值对数量
# 从 Redis 1.0.0 版本可用
SHUTDOWN

# 用户也可以显示地指示是否执行持久化
SHUTDOWN [save|nosave]
```

<br/>

---

<br/>

# 发布与订阅

Redis 的发布与订阅功能可以让客户端通过广播方式，将消息（message）同时发送给可能存在的多个客户端，并且发送消息的客户端不需要知道接收消息的客户端的具体信息。换句话说，发布消息的客户端与接收消息的客户端之间没有直接联系。

在 Redis 中，客户端可以通过订阅特定的频道（channel）来接收发送至该频道的消息，我们把这些订阅频道的客户端称位订阅者（subscriber）。一个频道可以有任意多个订阅者，而一个订阅者也可以同时订阅多个频道。除此之外，客户端还可以通过向频道发送消息的方式，将消息发送给频道的所有订阅者，我们把这些发送消息的客户端称位发送者（publisher）。

除了订阅频道之外，客户端还可以通过订阅模式（pattern）来接收消息：每当发布者向某个频道发送消息的时候，不仅频道的订阅者会收到消息，与频道匹配的所有模式的订阅者也会收到消息。

![两个频道和一个模式](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/16-3.png)

![消息被发送至频道订阅者以及匹配模式的订阅者](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/16-4.png)

<br/>
<br/>

## 向频道发送消息

通过执行 `PUBLISH` 命令，将消息发送到给定频道。

```redis
# 复杂度 O(N+M) N 为给定频道的订阅者数量，M 为服务器目前被订阅的模式总数量
# 从 Redis 2.0.0 版本可用
PUBLISH channel message
```

<br/>
<br/>

## 订阅频道

通过执行 `SUBSCRIBE` 命令，让客户端订阅给定的一个或多个频道。

```redis
# O(N) N 为用户输入的频道数量
# 从 Redis 2.0.0 版本可用
SUBSCRIBE channel [channel channel ...]
```

SUBSCRIBE 命令在订阅频道后向客户端返回一条订阅消息：

- 消息的第一个元素是 "subscribe"，表示这条消息由 SUBSCRIBE 命令引发的订阅消息而不是普通客户端发送的频道消息。
- 消息的第二个元素记录了被订阅频道的名字。
- 消息的最后一个元素是数字 1，表示订阅了一个频道。

<br/>

当客户端成为频道的订阅者之后，就会接收到来自被订阅频道的消息，我们把这些消息成为频道消息。频道消息由三个元素组成：

- 消息的第一个元素是 "message"，表示该消息是一条频道消息而非订阅消息。
- 消息的第二个元素为消息的来源频道。
- 消息的地三个元素为消息的正文内容。

<br/>
<br/>

## 退订频道

使用 `UNSUBSCRIBE` 命令退订指定的频道。如果不指定频道，则默认退订所有已订阅的频道。

```redis
# O(N) N 为服务器目前拥有的订阅者总数量
# 从 Redis 2.0.0 版本可用
UNSUBSCRIBE [channel channel ...]
```

客户端在每次退订频道之后，都会收到服务器发来的退订消息，这条消息由三个元素组成。

- 第一个元素是 "unsbuscribe"，表明该消息是一条由退订操作产生的消息。
- 第二个元素是被退订频道的名字。
- 地三个元素是客户端再植行退订操作之后，目前仍在订阅的频道数量。

<br/>
<br/>

## 订阅模式

使用 `PSUBSCRIBE` 命令，让客户端订阅给定的一个或多个模式。

传入 PSUBSCRIBE 命令的每个模式参数都可以是一个全局风格的匹配符，比如 "new.*" 模式匹配所有以 "new" 为前缀的频道。

```redis
# O(N) N 为用户给定的模式数量
# 从 Redis 2.0.0 版本可用
PSUBSCRIBE pattern [pattern pattern ...]

PSUBSCRIBE "news.*"
```

<br/>
<br/>

## 退订模式

使用 `PUNSUBSCRIBE` 命令退订模式。此命令允许用户退订多个模式，如果没有给定模式，默认将退订当前客户端已订阅的所有模式。

```redis
# O(N*M) N 为用户给定的模式数量，M 是服务器目前被订阅的模式总数量。 
# 从 Redis 2.0.0 版本可用
PUNSUBSCRIBE [pattern pattern pattern ...]

PSUBSCRIBE "news.*"
Reading messages... (press Ctrl-C to quit)
1) "psubscribe"
2) "news.*"
3) (integer) 1
```

<br/>
<br/>

## 查看发布与订阅的相关信息

使用 `PUBSUB` 命令，用户可以查看与发布、订阅有关的各种信息。

```redis
# 从 Redis 2.8.0 版本可用

# 查看被订阅的频道
# O(N) N 为服务器目前被订阅的频道总数量
PUBSUB CHANNELS [pattern]

# 查看频道的订阅者数量
# O(N) N 为用户给定的频道数量
PUBSUB NUMSUB [channel channel ...]

# 查看被订阅模式的总数量
# O(1)
PUBSUB NUMPAT
```

<br/>

---

<br/>

# 模块

Redis 为用户提供了流水线、事务和 Lua 脚本用于扩展 Redis 服务器的功能。但上述几种方法都有几个明显的缺陷：

- 这些扩展方式要求新功能必须基于 Redis 现有的数据结构或功能来实现，但有时候用户想要的数据结构和功能是无法简单地基于 Redis 提供的数据结构来实现的，即使实现了效率也不高。
- 目前提供的所有扩展方式在编程方面都过于复杂。
- 无论是事务还是 Lua 脚本，在性能方面都有一些损耗。这些损耗对于普通用户来说不是什么问题，但对于那些对性能敏感的用户来说却并非如此。

为了解决上述问题，并进一步提升 Redis 的可扩展性，Redis 4.0 版本添加了“模块”这个重要的新功能。

Redis 的模块功能允许开发者通过 Redis 开放的一簇 API，将 Redis 用作网络服务和数据存储平台。通过 C 语言在 Redis 之上构建任意复杂的、全新的数据结构、功能和应用。对于开发者来说，模块功能为他们提供了一个可以按需扩展的定制 Redis 的功能。

Redis 在官网网站（<https://redis.io/resources/modules/>）上罗列了目前可用的模块，这些模块通常由 C 语言或其他能够与 C 语言进行交互的语言实现。用户只需要下载模块的源码，编译它们，并在 Redis 里面载入编译好的模块（动态链接库文件），然后就可以像普通 Redis 命令一样是他模块实现的功能了。

<br/>
<br/>

## 模块的管理

了解如何编译模块、载入模块和卸载模块。

<br/>
<br/>

### 编译模块

为了方便用户尝试模块，Redis 在自身的源码中附带了几个测试用途的模块，位于源码的 `src/modules` 目录中。

```sh
ls /opt/redis/src/modules
gendoc.rb  helloacl.c  helloblock.c  hellocluster.c  hellodict.c  hellohook.c  hellotimer.c  hellotype.c  helloworld.c  Makefile
```

以 `.c` 结尾的 C 程序文件就是模块的源码，而以 `.so` 结尾的就是已经编译完毕的模块，也就是常见的 C 共享文件。一般来说，这些测试模块都会随着 Redis 服务器一并被编译，但如果情况不是这样，用户也可以通过 `make` 命令来编译这些模块。

<br/>
<br/>

### 载入模块

用户可以在 Redis 启动时，通过给定 `loadmodule` 选项或 `MODULE LOAD` 命令来指定想要载入的模块。一条命令只能载入一个模块，如需载入多个模块，请使用多个配置项或命令。

```conf
loadmodule <module_path>

loadmodule /opt/redis/src/modules/helloworld.so
```

```redis
# O(1)
MODULE LOAD module_path

MODULE LOAD /opt/redis/src/modules/helloworld.so
OK
```

<br/>
<br/>

### 列出已载入的模块

执行 `MODULE LIST` 命令来查看服务器目前已载入的所有模块。

```redis
# O(N) N 为已载入的模块数量
redis> MODULE LIST
1) 1) "name" --"name"字段的值记录了模块的名字
   2) "test" --这个模块名为"test"
   3) "ver" --"ver"字段的值记录了模块的版本号
   4) (integer) 1 --这个模块的版本号为1
2) 1) "name"
   2) "helloworld" --这个模块名为"helloworld"
   3) "ver"
   4) (integer) 1 --这个模块的版本号为1
```

<br/>
<br/>

### 卸载模块

使用 `MODULE UNLOAD` 命令卸载模块，被卸载的模块将立即失效并不再可用。

```redis
# O(1)
MODULE UNLOAD module_name

redis> MODULE UNLOAD HELLOWORLD
OK
```

<br/>
<br/>

## ReJSON模块介绍

JSON 作为一种常见的数据交换格式，在 Web 领域得到了广泛的应用。Redis 虽然可以存储文本和二进制文件，但它并没有提供对 JSON 数据的原生支持，无法直接通过命令操作存储在库中的 JSON 数据。用户只能在客户端程序进行处理。

ReJSON 模块通过 Redis 的模块机制，在 Redis 之上实现了对 JSON 数据结构的原生支持，用户可以直接在 Redis 数据库中存储、更新和获取 JSON 数据，就像操作其他原生 Redis 数据类型一样。

<br/>

编译和载入：

```sh
# 克隆
git clone xxx-rejson.git
cd rejson
make

# 载入
redis> MODULE LOAD /xxx/rejson/src/rejson.so
OK
```

使用：

```sh
# 创建JSON字符串
redis> JSON.SET module_name . '"ReJSON"'
OK

# 取值
redis> JSON.GET module_name
"\"ReJSON\""

# 查看该字符串的长度
redis> JSON.STRLEN module_name
(integer) 6

# 查看该JSON键的类型
redis> JSON.TYPE module_name
string
```

<br/>

---

<br/>

# 架构模式

<br/>
<br/>

## 架构模式和架构图

Redis 架构模式：

- 单机模式
  - 不保证可靠性
  - 由于 Redis 是单线程，单机性能受限于 CPU 的处理能力
- 主从模式
  - 没有保证高可用
  - 需要人工接入切换，切换后主地址变了
  - 没有解决主节点的写压力
  - 主节点的写能力和存储能力收到单机的限制
  - 客户端连接主节点地址
- 哨兵模式
  - 高可用的主从模式
  - 没有解决主节点的写压力
  - 动态扩容复杂
  - 客户端连接多个哨兵地址
- 集群模式
  - 高可用、可扩展、分布式、容错等特性
  - 去中心化，每个节点都可以保存数据和整个集群状态，每个节点都可与其它节点连接
  - 数据异步复制，无法保证强一致性
  - 有 16384（`2^14`）个 slots，数据与哈希槽关联，哈希槽与节点关联
  - 客户端连接多个主地址

<br/>

![单机模式](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/redis_single.png)

![主从模式](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/redis_master_slave.png)

![哨兵模式](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/redis_sentinel.png)

![集群模式](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/redis_cluster.png)

<br/>
<br/>

## 安装和部署

安装和部署 Redis 的一些注意事项：

- 源码编译请注意 GCC 版本
- 关闭透明大页
- 最大客户端
- 最大内存
- 配置文件、监听、端口和目录等
- 认证建议写入配置文件
- 开机自启动
- bind ip 地址的顺序如果变更后，哨兵会识别到 127.0.0.1，所以需要看看哨兵的配置，是否有 127 地址，如果有则去掉并重启进程

```sh
# 透明大页面
echo 'never' > /sys/kernel/mm/transparent_hugepage/enabled
echo 'never' > /sys/kernel/mm/transparent_hugepage/defrag

# 启动
/path/redis/src/redis-server /path/redis/conf/redis.conf
/path/redis/src/redis-sentinel /path/redis/conf/sentinel.conf

# 集群
# 它会自动给主添加从，从的配置文件中不需要配置主
/path/redis-xxx/src/redis-server /path/redis-xxx/master/redis-master.conf
/path/redis-xxx/src/redis-server /path/redis-xxx/slave/redis-slave.conf
redis-cli -a 密码 --cluster create --cluster-replicas=1 m1:port m2:port m3:port s1:port s2:port s3:port
```

<br/>

---

<br/>

# 主从复制

<br/>
<br/>

## 复制简介

复制功能是 Redis 提供的多机功能中最基础的一个，这个功能是通过主从复制（master-slave replication）模式实现的。它允许用户为存储着目标数据库的服务器创建出多个拥有相同数据库副本的服务器。其中存储目标数据库的服务器被称为主服务器（master），而存储数据库副本的服务器则被称为从服务器（slave 或 replica）。

也就是单主多从复制功能。

在默认情况下，复制模式下的主既可以写也可以读，而从只能读。

![](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/18-1.png)

![](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/18-2.png)

<br/>

对于开启了复制功能的主从服务器，主在每次执行写操作之后，都会与所有从进行数据同步，以此来将写操作产生的改动反映到各个从上。

![](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/18-5.png)

<br/>

Redis 的复制功能可以从性能、安全性和可用性三个方面提升整个 Redis 系统：

- 在性能方面，复制功能可一个系统的读性能带来线性级别的提升。
- 通过增加从服务器的数量，可以降低系统在遭受灾难故障时丢失数据的可能性。
- 通过复制功能和哨兵功能，可以为整个系统提供高可用特性。

<br/>
<br/>

## 将服务器设置为从服务器

用户可以执行复制命令，将接收这个命令的 Redis 服务器设置为另一个 Redis 服务器的从服务器。并在从上读取信息。

```redis
# 5.0 之前
SLAVEOF

# 5.0 之后
REPLICAOF

REPLICAOF host port
```

示例，将本机的 redis 12345 实例 设置为 本地的 redis 6379 的从。

```redis
127.0.0.1:12345> REPLICAOF 127.0.0.1 6379

127.0.0.1:6379> SET msg "hello"
127.0.0.1:6379> GET msg
"hello"

127.0.0.1:12345> GET msg
"hello"
```

<br/>
<br/>

### 通过配置项设置从服务器

除了使用命令，还可以使用配置项将 Redis 服务器设置为从服务器。

```conf
# REPLICATION
replicaof <host> <port>
```

<br/>
<br/>

### 取消复制

在从节点上执行命令（`REPLICAOF no one`），从从节点停止复制，重新变回主。但从服务器仍然会保留之前复制的数据。

```redis
127.0.0.1:12345> REPLICAOF no one
```

<br/>
<br/>

### 复制的其它信息

复杂度：`REPLICAOF` 命令本身的复杂度为 `O(1)`，但它引起的异步复制操作的复杂度为 `O(N)`，其中 N 为包含的键值对总数量。

版本要求：`REPLICAOF` 命令从 Redis 5.0.0 版本开始可用。`SLAVEOF` 命令从 1.0.0 版本开始可用，但从 5.0.0 版本开始逐渐废弃。

<br/>
<br/>

## 查看服务器的角色

可以通过 `ROLE` 命令来查看服务器当前的角色。

```redis
ROLE
```

<br/>

在主服务器执行 `ROLE` 命令，结果将返回一个由 3 个元素组成的数组。

- 第一个元素是字符串 master，表示服务器角色。
- 第二个元素是主服务的复制偏移量（replication offset），它是一个整数，记录了主服务器目前向复制数据流发送的数据数量。
- 地三个元素是一个数组，记录了这个主服务器下的所有从服务器。

```redis
127.0.0.1:6379> ROLE
1) "master" --这是一个主服务器
2) (integer) 155 --它的复制偏移量为155
3) 1) 1) "127.0.0.1" --第1个从服务器的IP地址为127.0.0.1
      2) "12345" --这个从服务器的端口号为12345
      3) "155" --它的复制偏移量为155
```

<br/>

在从服务器执行 `ROLE` 命令，结果将返回一个由 5 个元素组成的数组。

- 第一个元素是 slave，表示服务器角色。
- 第二/三个元素记录了主服务器的 IP 地址和端口
- 第四个元素是主从服务器的连接状态
  - none：主从尚未建立连接
  - connect：主从正在握手
  - connecting：主从成功建立了连接
  - sync：主从正在进行数据同步
  - connected：主从已经进入在线更新状态
  - unknown：主从连接状态未知
- 第五个元素是从服务器当前的复制偏移量

```redis
127.0.0.1:12345> ROLE
1) "slave" --这是一个从服务器
2) "127.0.0.1" --主服务器的IP地址
3) (integer) 6379 --主服务器的端口号
4) "connected" --主从服务器已经进入在线更新状态
5) (integer) 1765 --这个从服务器的复制偏移量为1765
```

<br/>

复杂度：`ROLE` 命令的复杂度为 `O(1)`。

版本要求：`ROLE` 命令从 Redis 2.8.12 版本开始可用。

<br/>
<br/>

## 数据同步

主从服务器需要通过数据同步机制来让两个服务器的数据库状态保持一致。

本节将对 Redis 主从服务器的数据同步机制进行介绍，理解同步机制的运作原理。

<br/>
<br/>

### 完整同步

当 Redis 服务器接收到 `REPLICAOF` 复制命令，开始对另一个服务器进行复制的时候，主从服务器会执行以下操作。

1. 主服务器执行 `BGSAVE` 命令，生成一个 RDB 文件，并使用缓冲区存储起 `BGSAVE` 命令之后执行的所有些命令。
2. 当 RDB 文件创建完毕，主服务器会通过套接字将 RDB 文件传送给从服务器。
3. 从服务器在接收完主服务器传送过来的 RDB 文件之后，就会载入这个 RDB 文件获取数据。
4. 当从服务器完成 RDB 文件载入操作，并开始上线接受命令请求时，主服务器会把之前存储在缓存区中的所有写命令发送给从服务器执行。

执行完命令后，主从服务器的数据将完全相同。

通过创建、传送并载入 RDB 文件来达成数据一致的步骤，我们称之为完整同步操作。每个从服务器在刚开始进行复制的时候，都需要与主服务器进行一次完整同步。

Redis 会在进行数据同步时重用 RDB 文件。

<br/>
<br/>

### 在线更新

为了让主从服务器的数据一致性可以保持下去，让它们一直拥有相同的数据，Redis 会对从服务器进行在线更新。

- 每当主服务器执行完一个写命令之后，它就会将相同的写命令（相同效果的命令）发送给从服务器执行。
- 从服务器只要接收并执行主服务器发来的写命令，就可以让自己的数据库重新与主服务器保持一致。
- 主要从服务器一直与主服务器保持连接，在线更新操作就会不断进行。

需要注意的是，在线更新是异步进行的。在主服务器执行完写命令之后，直到从服务器也执行完相同的写命令的这段时间里，主从服务器的数据库将出现短暂的不一致。因此要求强一致性的程序可能需要直接读取主而不是从。

此外，因为主可能在执行完写命令并向从发送写命令的过程中因故障而下线，所以从在主下线后可能会丢失主已经执行的一部分写命令，导致数据不一致的状态。

因为在线更新的异步本质，无法杜绝不一致。

<br/>
<br/>

### 部分同步

当因故障下线的服务器重新上线时，主从的数据通常已经不在一致，因此它们必须重新进行同步，让两者的数据再次回到一致状态。

在 Redis 2.8 版本以前，重同步是通过直接进行完整同步来实现，但这种方法非常浪费资源。

从 Redis 2.8 版本开始，使用新的重同步功能：

- 当一个 Redis 服务器成为另一个服务器的主时，它会把每个被执行的写命令都记录到一个特定长度的先进先出队列中。
- 当断线的从服务器尝试重连主的时候，主将检查从断线期间，被执行的那些写命令是否仍然保存在队列里。如果是，那么主就会直接把从缺失的那些写命令发送给从执行。从通过执行这些写命令就可以重新与主保持一致。这样就避免了重新进行完整同步的麻烦。
- 如果从缺失的那些写命令已经不存在与队列当中，那么主从将进行一次完整同步。

Redis 为这个队列设置的默认大小为 1MB，用户也可以根据自己的需要修改。配置选项为 `repl-backlog-size`。

<br/>
<br/>

## 无须硬盘的复制

创建 RDB 文件引起的大量硬盘写入可能会影响服务器性能。

为了解决这个问题，Redis 从 2.8.18 版本开始引入无须硬盘的复制特性（diskless replication）。启用了这个特性的主服务器在接收到复制命令时不会再在本地创建 RDB 文件，而是会派生出一个子进程，然后由子进程通过套接字直接将 RDB 文件写入从。

```conf
# 启用无硬盘复制特性
repl-diskless-sync yes
```

注意，无需硬盘的复制特性只是避免了在主上创建 RDB 文件，但仍需在从在上创建 RDB 文件。

<br/>
<br/>

## 降低数据不一致情况出现的概率

为了尽可能降低数据不一致的出现概率，Redis 从 2.8 开始引入了两个配置项：

```conf
min-replicas-max-lag <seconds>
min-replicas-to-write <numbers>
```

用户设置了这两个配置项后，主只会在从的数据大于等于 min-replicas-to-write 的值，并且从与主最后一次成功通信的间隔不超过 min-replicas-max-lag 的值时才会执行写命令。

示例，假设我们想让主旨在拥有至少 3 个从，并且这些从与主最后一次成功通信的间隔不超过 10s 的情况下才执行写命令。

```conf
min-replicas-max-lag 10
min-replicas-to-write 3
```

<br/>
<br/>

## 可写的从服务器

从 Redis 2.6 开始，从在默认状态下只允许执行读命令。这样是为了保证主从之间的数据一致性。

但在某些情况下，我们可能想要将一些不太重要或者临时性的数据存储在从中。可以通过配置项 `replica-read-only` 来打开从的写功能：

```conf
# 启用从的写功能
replica-read-only yes
```

使用可写从的注意事项：

- 在主从都可写的情况下，程序必须将写命令发送到正确的服务器上，不能把需要在主执行的写命令发送给从执行。也不能把从执行的写命令发送给主，否则就会出现数据错误。
- 从执行写命令得到的数据，可能会被主发送的写命令覆盖。基于这个原因，从上执行的写命令应该避免与主发生键冲突。
- 当从与主进行完整同步时，从所包含的所有数据都将被清空。
- 为了减少内存占用，降低键冲突发生的可能性，并确保主从的数据同步可以顺利进行，客户端写入从的数据应该在使用完毕后尽快删除（如给键设置过期时间）。

<br/>
<br/>

### 使用从服务器处理复杂计算操作

Redis 的数据相关命令基本可以划分为两类：

- 简单的读写操作：如 GET、SET、ZADD 命令等，这种命令只需要对数据库进行简单的读写，它们的执行速度一般都非常快。
- 比较复杂的计算操作：如 SUNION、ZINTERSTORE、BITOP 命令等，这种命令需要对数据库中的元素进行聚合计算，并且随着元素数量的增加，计算耗费的时间也会增加，因此这种命令的执行速度一般都比较慢。

从效率上看，在同一个 Redis 服务器上同时处理以上两种命令，那么执行复杂命令产生的阻塞时间将导致简单名单的延迟明显增加。

为此，我们可以让主处理简单读写命令，从处理复杂计算命令。

如果只使用一个从处理复杂计算命令不够快，可以继续增加从，直到从处理复杂计算命令的速度和延迟达到我们的要求为止。

<br/>
<br/>

## 脚本复制

Redis 传播 Lua 脚本的具体方法。Redis 服务器拥有两种不同的脚本复制模式：

- 第一种是从 Redis 2.6 开始支持的脚本传播模式（whole script replication）
- 另一种是从 Redis 3.2 开始支持的命令传播模式（script effect replication）

<br/>
<br/>

### 脚本传播模式

脚本传播是 Redis 复制脚本时默认使用的模式。

处于脚本传播模式的主会将被执行的脚本及其参数（也就是 EVAL 命令）复制到 AOF 文件和从。因为带有副作用的函数在不同服务器上运行时可能会产生不同的结果，从而导致主从服务器不一致，所以在这一模式下执行的脚本必须是纯函数。换句话说，对于相同的数据集，相同的脚本以及参数必须产生相同的效果。

为了保证脚本的纯函数性质，Redis 对处于脚本传播模式的 Lua 脚本设置了一下限制：

- 脚本不能访问 Lua 的时间模块、内部状态或除给定参数之外的其他外部信息。
- 在 Redis 的命令当中，存在着一部分带有随机性质的命令，这些命令对于相同的数据集以及相同的参数可能会返回不同的结果。脚本在执行此类随机命令之后，尝试继续执行写命令，那么 Redis 将拒绝执行该命令并返回一个错误。带有随机性质的 Redis 命令：SPOP、SPANDMEMBER、SCAN、SSCAN、ZSCAN、HSCAN、RANDOMKEY、LASTSAVE、PUBSUB、TIME。
- 当用户在脚本中调用 SINTER、SUNION、SDIFF、SMEMBERS、HKEYS、HVALS、KEYS 这 7 个会以随机返回结果元素的命令时，为了消除其随机性质，Lua 环境在返回这些命令的结果之前会先对结果中包含的元素进行排序，以此来确保命令返回的元素总是有序的。
- Redis 会确保每个被执行的脚本都拥有相同的随机生成器种子，这意味着如果用户不主动修改这一种子，那么所有脚本在默认情况下产生的伪随机数都将是相同的。

```redis
# 脚本传播模式的主执行示例命令
EVAL "redis.call('SET', KEYS[1], 'hello world');redis.call('SET', KEYS[2], 10086);redis.call('SADD', KEYS[3], 'appl
e', 'banana', 'cherry')" 3 'msg' 'number' 'fruits'

# 主将向从发送完全相同的 EVAL 命令
EVAL "redis.call('SET', KEYS[1], 'hello world');redis.call('SET', KEYS[2], 10086);redis.call('SADD', KEYS[3], 'appl
e', 'banana', 'cherry')" 3 'msg' 'number' 'fruits'
```

<br/>
<br/>

### 命令传播模式

处于命令传播模式的主会将执行脚本产生的所有写命令用事务包裹起来，然后将事务复制到 AOF 文件和丛。因为命令传播模式复制的是写命令而不是脚本本身，所以即使脚本本身包含副作用，主给所有从复制的写命令仍然是相同的。因此处于命令传播模式的主能够执行带有副作用的非纯函数脚本。

除了脚本可以不是纯函数外，命令传播模式对 Lua 环境还有以下放松：

- 用户可以在执行 RANDOMKEY、SRANDMEMBER 等带有随机性质的命令之后继续执行写命令。
- 脚本的伪随机数生成器在每次调用之前，都会随机地设置种子。换句话说，被执行的每个脚本在默认情况下产生的伪随机数都是不一样的。

为了开启命令传播模式，用户在使用脚本执行任何写操作之前，需要现在脚本中调用 `redis.replicate_commands()` 函数。

```redis
# 主执行以下示例命令
EVAL "redis.replicate_commands();redis.call('SET', KEYS[1], 'hello world');redis.call('SET', KEYS[2], 10086);redis.
call('SADD', KEYS[3], 'apple', 'banana', 'cherry')" 3 'msg' 'number' 'fruits'

# 主向从复制以下命令
MULTI
SET "msg" "hello world"
SET "number" "10086"
SADD "fruits" "apple" "banana" "cherry"
EXEC
```

<br/>
<br/>

### 选择性命令传播

Redis 允许用户在脚本中选择性地打开或者关闭命令传播功能。可以通过在脚本中调用 `redis.set_repl()` 函数并向它传入值来完成。

- `.redis.REPL_ALL`：默认值，将命令传播至 AOF 文件以及所有从。
- `.redis.REPL_AOF`：只将写命令传播到 AOF 文件。
- `.redis.REPL_SLAVE`：只将写命令传播到所有从。
- `.redis.REPL_NONE`：不传播写命令。

<br/>
<br/>

### 模式的选择

一般来说，用户可以根据以下情况来判断应该使用哪种复制模式：

- 如果脚本的体积不大，执行的计算也不多，但却会产生大量命令调用，那么使用脚本传输模式可以有效地节约网络资源。
- 如果脚本的体积很大，执行的计算非常多，但是只会产生少量的命令调用，那么使用命令传播模式可以重用已有的计算结果来节约计算资源和网络资源。

<br/>
<br/>

## 服务器相关命令

Redis 服务器相关的命令。

```sh
# 查看 redis 支持的命令
COMMAND
# 获取特定命令的信息
COMMAND INFO AUTH

# 认证
AUTH 密码

# 查看信息
INFO
# 查看复制相关信息
INFO REPLICATION

# 实时打赢出 redis 接收到的命令，调试用
MONITOR

# 获取键的调试信息
DEBUG object 键名称

# 客户端连接相关
CLIENT LIST
CLIENT KILL [ip:port] [ID client-id]

# 配置相关
CONFIG GET *
CONFIG GET slave-priority
CONFIG SET 参数 值
CONFIG REWRITE

# 最近一次成功将数据保存到磁盘上的 UNIX 时间
LASTSAVE

# 慢日志
SLOWLOG

# 角色
ROLE

# 将当前实例添加为主的从
SLAVEOF 主 端口
# 从节点从主中下线，自己独立为主
SLAVE NO ONE

# 关闭
SHUTDOW NOSAVE/SAVE
```

<br/>

---

<br/>

# 哨兵模式

因为 Redis 支持主从复制特性，所以我们可以对下线的 Redis 主服务器实施故障转移。

举个例子。假设我们有一个主（`127.0.0.1:6379`），两个从（127.0.0.1:6380, 127.0.0.1:6381）。如果某一时刻，主 6379 因为故障而下线。我们可以向 6380 发送 `REPLICAOF no one` 将其转换为主，然后向从 6381 发送 `REPLICAOF 127.0.0.1:6380`，让它去复制新的主 6380。

监视多台 Redis 服务器，并在有需要的时候对下线的主服务器实施故障转移，这并不是一件容易的事。为了给 Redis 提供自动化的故障转移功能，提高主从的可用性，Redis 提供了 Sentinel（哨兵）工具。哨兵通过心跳检测的方式监视多个主以及它们的从，并在某个主下线时自动对其实施故障转移。

简单来说，如果哨兵监视 主 6379 和从 6380 和 6381，那么在 6379 因为故障而下线时，哨兵就会把从的其中一个转换为主，并让其它从去复制新的主，这个过程完全不需要人工介入。

<br/>
<br/>

## 启动哨兵

示例命令:

```sh
redis-sentinel /etc/redis-sentinel.conf
# 或
redis-server /etc/redis-sentinel.conf --sentinel
```

最基础的哨兵配置文件:

```conf
# sentinel monitor <master-name> <ip> <port> <quorum>
# quorum 用于判定主下线所需的哨兵数量
setinel monitor redis01 127.0.0.1 6379 1
```

![哨兵](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/19-2.png)

<br/>

哨兵首先监视我们指定的主 6379，又自动发现并监视了它的两个从 6380 和 6381。

因为哨兵开始监视主后，就会去获取被监视主的从名单，并根据名单对从实施监视，整个过程是完全自动的，用户只需要输入待监视的主的地址。

除此之外，哨兵还会对每个被监视的主从实施心跳检测，并记录各个服务器的在线状态、响应速度等信息。当哨兵发现被监视的主进入下线状态时，它就会开始对下线的主实施故障转移。

举个例子，如果我们故意杀死主 6379，那么哨兵将检测到 6379 已下线，并从 6380 和 6381 这两个从之间选出一个新的主，然后重新构建一个主从连接。

<br/>
<br/>

### 可以同时监视多个主

一个哨兵可以监视任意数量的主，而不仅仅是一个主。但为了避免混淆，一般是一个环境配置一套 Redis 和 Redis Sentinel。

如果想要使用哨兵去监视多个主，只需要在配置文件中指定多个 `sentinel monitor` 选项即可，并为每个被监视的主服务器设置不同的名字。

```conf
sentinel monitor redis01 127.0.0.1 6379 1
sentinel monitor redis02 192.168.0.10 6379 1
sentinel monitor redis03 192.168.1.10 6379 1
```

<br/>
<br/>

### 处理重新上线的旧的主

哨兵在对下线的主实施故障转移之后，仍然会继续对它进行心跳检测。当这个服务器重新上线时，哨兵会把它转换为当前主的从。

<br/>
<br/>

### 设置从服务器的优先级

用户可以通过 `replica-priority`（5.0 之前的版本是 `slave-priority`） 配置项来设置各个从服务器的优先级。优先级较高的从在哨兵选择主的时候会被优先选择。

这个值默认是 100，值越小，从的优先级就越高。优先级为 0 的从永远不会被选为主。用户可以通过设置此配置项将不适合用作主的从排除在新主的候选名单之外。

通过这个配置项，可以给性能较高的从服务器设置较高的优先级来尽可能地保证新主的性能。因为主在下线并重新上线之后也会变成从，所以用户也应该为主设置相应的优先级。

```conf
# 5.0
replica-priority 100

# 5.0-
slave-priority 100
```

<br/>
<br/>

### 新主服务器的挑选规则

当哨兵需要在多个从中选择一个作为新的主时，首先会根据以下规则从候选名单中剔除不符合条件的从：

1. 否决所有已经下线以及长时间没有回复心跳检测的疑似已下线的从。
2. 否决所有长时间没有与主通信，数据状态过时的从。
3. 否决所有优先级为 0 的从。

然后根据以下规则，在剩余的从中选出新的主：

1. 优先级最高的从获胜。
2. 如果优先级最高的从有多个，那么复制偏移量最大的那个获胜。
3. 如果符合上述条件的从有多个，那么选出它们当中运行 ID（服务器启动时自动生成的随机数）最小的那一个。

<br/>
<br/>

## 哨兵网络

前面的示例只使用了单个哨兵，但在实际使用中，可以使用多个哨兵组建一个分布式的哨兵网络，网络中的各个哨兵可以通过互通消息来更加准确地判断服务器的状态。在一般情况下，只要哨兵网络中有半数以上的哨兵在线，故障转移就可以继续进行。

当哨兵网络中的其中一个哨兵认为某个主已经下线时，它会将这个主标记为主观下线（Subjectively Down, SDOWN），然后会询问网络中的其他哨兵，是否也认为该服务器已下线。当同意主已下线的哨兵数量达到 `sentinel monitor` 配置项中 `quorum` 参数所指定的数量时，哨兵就会将相应的主标记为客观下线（Objectively Down, ODOWN），然后开始对其进行故障转移。

因为哨兵网络使用客观下线机制来判断一个主是否真的已经下线了，所以为了让这种机制能够有效地运作，用户需要将 `quorum` 参数的值设置为哨兵数量的半数以上，从而形成一个少数服从多数的投票机制。

举个栗子，在一个拥有 3 个哨兵的网络中，`quorum` 参数的值至少需要设置成 2。在一个拥有 5 个哨兵的网络中，`quorum` 参数的值至少需要设置成 3。

<br/>
<br/>

### 组建哨兵网络

组建哨兵网络很简单，与启动单个哨兵的方法一样。用户只需要启动多个哨兵（并使用 `sentinel monitor` 监视相同的主），那些监视相同主的哨兵会自动发现对方，并组成哨兵网络。

![哨兵网络](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/19-3.png)

<br/>
<br/>

## 哨兵管理命令

一些常用的哨兵管理命令，连接到 Redis Sentinel 的命令。

命令从 Redis 2.8.0 版本可用。

<br/>
<br/>

### 获取所有被监视的主服务器的信息

```redis
# 复杂度 O(N)，N 为主的数量
SENTINEL masters
```

<br/>
<br/>

### 获取指定被监视主服务器的信息

```redis
# 复杂度 O(1)
SENTINEL master <master-name>
```

<br/>
<br/>

### 获取被监视主服务器的从服务器的信息

```redis
# 复杂度 O(N)，N 为从的数量
SENTINEL slaves <master-name>
```

<br/>
<br/>

### 获取其他哨兵的信息

```redis
# 复杂度 O(N)，N 为正在监视主的哨兵数量
SENTINEL sentinels <master-name>
```

<br/>
<br/>

### 获取给定主的 IP 和端口

```redis
# 如果哨兵进行了故障转移操作，那么将返回新的主
# 复杂度 O(1)
SENTINEL get-master-addr-by-name <master-name>
```

<br/>
<br/>

### 重置主服务器的状态

```redis
# 复杂度 O(N)，N 为被重置的主的数量
SENTINEL reset <pattern>
```

<br/>
<br/>

### 强制执行故障转移

用户可以强制对指定的主实施故障转移，就好像它已经下线了一样。

接收到这一命令的哨兵会直接对主执行故障转移操作，而不会像平时那样，先在哨兵网络中进行投票，然后再根据投票结果决定是否执行故障转移操作。

```redis
# 复杂度 O(1)
SENTINEL failover <master-name>
```

<br/>
<br/>

### 检查可用哨兵的数量

检查哨兵网络中当前可用的哨兵数量是否达到了判断主客观下线并实施故障转移所需的数量。

```redis
# 复杂度 O(1)
SENTINEL ckquorum <master-name>
```

<br/>
<br/>

### 强制写入配置文件

让哨兵将它的配置文件重新写入硬盘。

因为哨兵在被监视服务器的状态发生变化时就会自动重写配置文件，所以这个命令的作用就是在配置文件基于某些原因或错误而丢失时，立即生成一个新的配置文件。此外，当哨兵的配置项发生变化时，哨兵内部也会使用这个命令创建新的配置文件来替换原有的配置文件。

比如我手动删掉哨兵配置文件，然后执行此命令，这样哨兵会重新生成一个新的配置文件。

需要注意的是，只有接收到此命令的哨兵才会重写配置文件，哨兵网络中的其他哨兵并不会受到这个命令的影响。

```redis
# 复杂度 O(1)
SENTINEL flushconfig
```

<br/>
<br/>

## 在线配置哨兵

在 Redis 2.8.4 版本之前，哨兵只能通过载入配置文件来修改配置项。手动修改配置文件并重启的做法不仅麻烦、容易出错，而且在运行过程中停止哨兵还可能导致主失去有效的监控。

Redis 从 2.8.4 版本开始为 `SENTINEL` 命令添加了一组子命令，这些子命令可以在线地修改哨兵对于被监视主服务器的配置项，并把修改之后的配置项保存到配置文件中。整个过程不需要停止哨兵，也不需要手动修改配置，非常方便。

<br/>
<br/>

### 监视给定主服务器

让哨兵开始监视一个新的主服务器。此命令就是 `sentinel monitor` 配置项的命令版本。

```redis
# 复杂度 O(1)
SENTINEL monitor <master-name> <ip> <port> <quorum>
```

<br/>
<br/>

### 取消对给定主服务器的监视

接收到此命令的哨兵会停止对给定主的监视，并删除哨兵内部以配置文件中与给定主有关的所有信息。

```redis
# 复杂度 O(1)
SENTINEL remove <master-name>
```

<br/>
<br/>

### 修改哨兵配置项的值

在线修改哨兵配置文件中与主相关的配置选项值。

```redis
# 复杂度 O(1)
SENTINEL set <master-name> <option> <value>
```

<br/>
<br/>

### 使用在线配置命令的注意事项

在线配置命令只会对接收到命令的单个哨兵生效，并不会对同一个哨兵网络中的其他哨兵产生影响。

哨兵网络中的各个哨兵可以拥有不同的 `quorum` 值。

<br/>
<br/>

## 使用redis-py管理哨兵

<br/>

---

<br/>

# 集群

Redis Cluster 是 Redis 3.0 版本开始引入的功能，它给用户带来了在线扩展 Redis 系统读写性能的能力。而 Redis 更是在集群原有功能的基础上，进一步添加了更多新功能，使得整个集群系统更简单、易用和高效。

<br/>
<br/>

## 基本特性

Redis 集群与单机版 Redis 服务器一样，也提供了主从复制功能。在 Redis 集群中，各个 Redis 服务器被称为节点（node），其中主节点（master node）负责处理客户端发送的读写命令请求，而从节点（replica/slave node）则负责对主节点进行复制。

除了复制功能外，Redis 集群还提供了类似于单机版 Redis Sentinel 的功能，以此来为集群提供高可用特性。简单来说，集群中的各个节点将相互监视各自的运行状况，并在某个主节点下线时，通过提升该节点的从节点为新主节点来继续提供服务。

![集群及其节点](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/20-1.png)

<br/>
<br/>

### 分片与重分片

与单机版 Redis 将整个数据库放在同一台服务器上的做法不同，Redis 集群通过将数据库分散存储到多个节点上来平衡各个节点的负载压力。

Redis 集群会将整个数据库空间划分为 16384 个槽（slot）来实现数据分片（sharding），而集群中的各个主节点则会分别处理其中的一部分槽。当用户尝试将一个键存储到集群中时，客户端会先计算处键所属的槽，接着在记录集群节点槽分布的映射表中找出处理该槽的节点，最好再将键存储到相应的节点中。

当用户想要向集群添加新节点时，只需要向 Redis 集群发送几条简单的命令，集群就会将相应的槽以及槽中存储的数据迁移到新节点。与此类似，当用户想要从集群中移除已存在的节点时，被移除的节点也会将自己负责处理的槽以及槽中数据转交给集群中其他节点负责。最重要的是，无论是向集群添加还是移除节点，整个重分片（reshard）过程都可以在线进行，集群无需停机。

![集群的分片实现](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/20-2.png)

<br/>
<br/>

### 高性能

Redis 集群采用无代理模式，客户端发送的所有命令都会直接交由节点执行，并且对于经过优化的集群客户端来说，客户端发送的命令在绝大部分情况下都不需要实施转向，因此集群中执行命令的性能与单机执行命令的性能非常接近。

从理论上来说，集群每增加一倍数量的主节点，集群对于命令请求的处理性能就会提高一倍。

<br/>
<br/>

## 搭建集群

搭建一个 Redis 集群有两种方法：

- 使用源码附带的集群自动搭建
- 使用配置文件手动搭建

<br/>
<br/>

### 快速搭建集群

Redis 在它的源码中附带了自动搭建程序 `create-cluster`，可以帮助快速构建起一个完整可用的集群以供用户测试。

示例，快速创建 6 个本机端口号为 30001-30006 的节点。它会根据现有的节点制定出一个相应的角色和槽分配计划，然
后询问你的意见。

```sh
./create-cluster start

./create-cluster create

./create-cluster stop

./create-cluster clean
```

![自动搭建的集群](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/20-3.png)

<br/>
<br/>

### 手动搭建集群

生产环境中使用相应的配置文件来启动 Redis 集群，并使用集群管理命令对集群进行配置和管理。

为了保证集群的各项功能可以正常运转，一个集群至少需要 3 个主节点和 3 个从节点。

下面示例创建一个 5 主 5 从的 Redis 集群。

```sh
# 创建各个节点的目录
mkdir my-cluster && cd my-cluster
mkdir node{1..10}

# 配置每个节点的配置
cluster-enabled yes
port 3000[1-10]

# 启动
redis-server redis.conf

# 虽然我们已经启动了 10 个集群节点，但由于这些集群并未互联互通，所以它们都只在各自的集群之内。
# 接下来我们需要连接这 10 个节点并为它们分配槽。
redis-cli --cluster create 127.0.0.1:30001 127.0.0.1:30002 127.0.0.1:30003 127.0.0.1:30004 127.0.0.1:30005 127.0.0.1:30006 127.
0.0.1:30007 127.0.0.1:30008 127.0.0.1:30009 127.0.0.1:30010 --cluster-replicas 1
# create 子命令将制定节点角色和槽分配计划
# 比如把 3000[1-5] 设置为主，并把 16384 个槽平均分配给这 5 个节点。30006-30010 则指派给主的从
```

![手动搭建的集群](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/20-5.png)

<br/>
<br/>

## 散列标签

默认情况下，Redis 将根据用户输入的整个键计算出改建所属的槽，然后将键存储到相应的槽中。但在某些情况下，出于性能方面的考虑，或为了在同一个节点上对多个相关联的键执行批量操作，我们会想将一些原本不属于同一个槽的键放到相同的槽里面。

为了满足这一需求，Redis 为用户提供了散列标签（hash tag）功能，该功能会找出键中第一个被大括号 `{}` 包围并且非空的字符串子串（substring），然后根据子串计算出该键所属的槽。这样，即使两个键不属于同一个槽，但只要它们拥有相同的被包围子串，那么程序计算出的散列值就是一样的，因此 Redis 集群就会把它们存储到同一个操作。

比如，我们有一批用户数据，它们全部以 `user::` 为前缀，其中键 `user::10086` 和 `user::10087` 原本应该分别术语两个不同的槽。但如果我们对这两个键使用散列标签功能，那么这两个键将被分配至同一个槽。

```redis
127.0.0.1:30002> CLUSTER KEYSLOT {user}::10086
(integer) 5474

127.0.0.1:30002> CLUSTER KEYSLOT {user}::10087
(integer) 5474
```

<br/>
<br/>

## 从节点的读命令执行权限

集群的从节点在默认情况下只会对主节点进行复制，不会处理客户端发送的任何命令请求。每当从节点接收到命令请求的时候，它只会向客户端发送转向消息，引导客户端向某个主节点重新发送命令请求。

举个栗子，对于主节点 30001 和它的从节点 30005 来说，如果我们向从节点发送以下命令，那么从节点会把客户端转向至主节点，然后在执行命令。

```redis
127.0.0.1:30005> GET num
-> Redirected to slot [2765] located at 127.0.0.1:30001
"10086"
127.0.0.1:30001>
```

<br/>

Redis 向用户提供了 `READONLY` 和 `READWRITE` 两个命令，它们可以临时打开或关闭客户端在从节点上执行读命令的权限。

命令只对执行了该命令的客户端有效，并不影响正在访问相同从节点的其他客户端。

两个命令的复杂度都是 `O(1)`。

```redis
# READONLY：打开读命令执行权限
127.0.0.1:30005> READONLY
OK
127.0.0.1:30005> GET num
"10086"

# READWRITE：关闭读命令执行权限
127.0.0.1:30005> READWRITE
OK
127.0.0.1:30005> GET num
-> Redirected to slot [2765] located at 127.0.0.1:30001
"10086"
127.0.0.1:30001>
```

<br/>
<br/>

## 集群管理工具

Redis 官方提供了两种工具来管理集群，一种是 `redis-cli` 客户端，另一种是内置的命令。

<br/>
<br/>

### redis-cli客户端工具

```sh
redis-cli --cluster --help

# 以下都是子命令

# 查看集群信息
redis-cli --cluster info <ip>:<port>

# 检查集群的配置是否正确，以及全部槽是否已经全部指派给了主节点。
redis-cli --cluster check <ip>:<port>

# 修复槽错误，当集群在重分片、负载均衡或槽迁移的过程中出现错误，可以让操作涉及的槽重新回到正常状态。
redis-cli --cluster fix <ip>:<port>

# 重分片，用户可以将指定数量的槽从原节点迁移至目标节点，被迁移的槽将交由后者负责，并且槽中已有的数据也会陆续从原节点转移到目标节点。
redis-cli --cluster reshard <ip>:<port>

# 负载均衡，允许用户在有需要时重新分片各个节点负责的槽数量
redis-cli --cluster rebalance <ip>:<port>

# 添加节点，将给定的新节点添加到已有的集群
redis-cli --cluster add-node <new_host>:<port> <existing_host>:<port>
# 默认情况下, 添加的新节点将作为主节点存在
--cluster-slave --cluster-master-id <id>

# 移除节点
redis-cli --cluster del-node <ip>:<port> <node_id>

# 执行命令，在集群上的所有节点上执行给定的命令
redis-cli --cluster call host:port command arg...

# 设置超时时间
set-timeout <host>:<port> <milliseconds>

# 导入数据，将给定单机 Redis 服务器的数据导入集群中。
import <node-host>:<port> # 集群入口节点的IP地址和端口号
--cluster-from <server-host>:<port> # 单机服务器的IP地址和端口号
--cluster-copy # 使用复制导入
--cluster-replace # 覆盖同名键
```

<br/>
<br/>

### 集群管理命令

Redis 还提供了一组以 `CLUSTER` 开头的集群命令。

需要注意的是，`redis-cli --cluster` 实际上就是由 `CLUSTER` 命令实现的，两者的命令是对应的。

```redis
# 以下命令都在 redis 客户端里执行
# 如 127.0.0.1:30001> 里

# 将给定节点添加到当前集群
# 复杂度 O(1)
CLUSTER MEET ip port

# 查看集群内所有节点的相关信息
# 复杂度 O(N)，N 为集群包含的节点数量
CLUSTER NODES

# 查看当前节点的运行 ID
# 复杂度 O(1)
CLUSTER MYID

# 查看集群信息
# 复杂度 O(1)
CLUSTER INFO

# 从集群中移除节点
# 复杂度 O(1)
CLUSTER FORGET node-id
# 等价于 redis-cli --cluster call 127.0.0.1:30001 CLUSTER FORGET node-id

# 将节点变为从节点
# 集群只允许对主节点进行复制
# 命令本身的复杂度是 O(1)，但它引起的异步复制操作的复杂度为 O(N)，其中 N 为主节点数据库所包含的键值对总数量。
CLUSTER REPLICATE master-id

# 查看给定节点的所有从节点
# REPLICAS 是 5.0 版本后的，之前的是 SLAVES
# 复杂度 O(N)
CLUSTER REPLICAS node-id


# 强制执行故障转移
# 复杂度 O(1)
CLUSTER FAILOVER
# FORCE 和 TAKEOVER 选项来改变故障转移的行为
# 在给定了FORCE选项时，从节点将在不尝试与主节点进行握手的情况下，直接实施故障转移。这种做法可以让用户在主节点已经下线的情况下立即开始故障转移。
# 需要注意的是，即使用户给定了FORCE选项，从节点对主节点的故障转移操作仍然要经过集群中大多数主节点的同意才能够真正执行。但如果用户给定了TAKEOVER选项，那么从节点将在不问集群中其他节点意见的情况下，直接对主节点实施故障转移。

# 重置节点
# 接受SOFT和HARD两个可选项作为参数，默认是 SOFT 选项
# 此命令只能在数据库为空的节点上执行，否则将返回错误
# 复杂度 O(N)
CLUSTER RESET
# 此命令将对节点执行以下操作：
# 遗忘该节点已知的其他所有节点
# 撤销指派给该节点的所有槽，并清空节点内部的槽-节点映射表
# 如果执行该命令的节点是一个从节点，那么将它转换成一个主节点
# 如果执行的是硬重置，那么为节点创建一个新的运行ID
# 如果执行的是硬重置，那么将节点的纪元和配置纪元都设置为0
# 通过集群节点配置文件的方式，将新的配置持久化到硬盘上
```

![集群组成的过程](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/20-6.png)

![两个集群合并为一个集群的过程](https://raw.githubusercontent.com/zhang21/images/master/cs/database/redis/20-7.png)

<br/>
<br/>

## 槽管理命令

<br/>
<br/>

### 查看槽与节点之间的关联信息

获知各个槽与集群节点之间的关联信息。

```redis
# 复杂度 O(N)
CLUSTER SLOTS
```

命令会返回一个嵌套数组，数组中的每个项记录了一个槽范围（slot range）及其处理者的相关信息：

- 槽范围的起始槽和结束槽
- 负责处理这些槽的主节点信息
- 零个或多个主节点属下从节点的信息

```redis
# 示例
127.0.0.1:30001> CLUSTER SLOTS
1) 1) (integer) 0 --起始槽
2) (integer) 5460 --结束槽
3) 1) "127.0.0.1" --主节点信息
2) (integer) 30001
3) "9e2ee45f2a78b0d5ab65cbc0c97d40262b47159f"
4) 1) "127.0.0.1" --从节点信息
2) (integer) 30005
3) "f584b888fcc0e7648bd838cb3b0e2d1915ac0ad7"
```

<br/>
<br/>

### 把槽指派给节点

将一个或任意多个槽指派给当前节点处理。

需要注意的是，只能对未被指派的槽执行，如果槽已经被指定，将返回错误。

```redis
# 复杂度 O(N)
CLUSTER ADDSLOTS slot [slot ...]
```

示例，将尚未被指派的 0-5 指派给节点 30001 负责：

```redis
127.0.0.1:30001> CLUSTER ADDSLOTS 0 1 2 3 4 5
OK
```

<br/>
<br/>

### 撤销对节点的槽指派

撤销对节点的槽指派。

需要注意的是，撤销一个未指派的槽将引发错误。

```redis
# 复杂度 O(N)
CLUSTER DELSLOTS slot [slot ...]
```

示例，撤销对该节点的槽 0-5 的指派：

```redis
127.0.0.1:30001> CLUSTER DELSLOTS 0 1 2 3 4 5
OK
```

<br/>
<br/>

### 撤销对节点的所有槽的指派

通过在一个节点上执行此命令，可以撤销对该节点的所有槽指派，让它不再负责处理任何槽。

需要注意，用户在执行此命令前，必须确保节点的数据库为空，否则节点将拒绝执行命令并返回错误。

```redis
# 复杂度 O(N)
CLUSTER FLUSHSLOTS
```

<br/>
<br/>

### 查看键所属的槽

通过对给定键执行此命令，可以知道改键所属的槽。

最后，正如前面所说，带有相同散列标签的键将被分配到相同的槽。

```redis
# 复杂度 O(N)
CLUSTER KEYSLOT key
```

<br/>
<br/>

### 查看槽包含的键数量

查看给定槽包含的键数量。

请注意，如果这个槽不是这个节点负责处理，那么将返回 0。所以请求正确的节点上执行。

```redis
# 复杂度 O(N)
CLUSTER COUNTKEYSINSLOT slot
```

<br/>
<br/>

### 获取槽包含的键

获取指定槽包含的键。

请注意，只能获取当前节点包含的键。所以请在正确的节点上执行。

```redis
# slot 指定槽，count 指定允许返回的最大键数量
# 复杂度 O(log(N) + M)，N 为键数量，M 
CLUSTER GETKEYSINSLOT slot count
```

<br/>
<br/>

### 改变槽的状态

改变给定槽在节点中的状态，从而实现节点之间的槽迁移以及集群重分片。

```redis
# 复杂度都是 O(1)

# 导入槽，让节点的指定槽进入导入中状态，处于该状态的槽允许从源节点中导入槽数据
CLUSTER SETSLOT slot IMPORTING source-node-id

# 迁移槽，让节点的指定操进入迁移中状态，处于该状态的槽允许向目标节点转移槽数据
CLUSTER SETSLOT slot MIGRATING destination-node-id

# 将槽指派给节点，在将槽数据从源节点迁移到目标节点后，用户可以在集群的任一节点执行此命令，正式将槽指派给目标节点负责
# 目标节点在接收到这一信息之后将移除给定槽的导入中状态，而源节点在接收到这一信息后将移除给定槽的迁移中状态
CLUSTER SETSLOT slot NODE node-id

# 移除槽的导入/迁移状态
# STABLE 子命令的唯一作用，就是在槽迁移出错或者重分片出错时，手动移除相应节点的槽状态
CLUSTER SETSLOT slot STABLE
```

<br/>

---

<br/>

# 安装方法

可以访问 [TryRedis](try.redis.io) 网站查看教程。

<br/>
<br/>

## 在Linux上安装

通过源码编译安装。

```sh
# 安装依赖工具
yum/apt install gcc make git

# 克隆项目
git clone https://github.com/antirez/redis.git

# 编译并测试
cd redis
make
make test

# 启动
./src/redis-server
```

