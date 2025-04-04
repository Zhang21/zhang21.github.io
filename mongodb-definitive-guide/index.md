# MongoDB权威指南


MongoDB 相关笔记。

<!--more-->

---

参考:

- [MongoDB 权威指南](https://book.douban.com/subject/35688800/)
- [MongoDB 官方文档](https://www.mongodb.com/zh-cn/docs/)

<br/>
<br/>

# 简介

MongoDB中没有预定义模式，文档键值的类型和大小不是固定的，因此按需添加或删除字段变得很容易。

MongoDB的设计采用了横向扩展。面向文档的数据模型使跨多台服务器拆分数据更加容易。MongoDB会自动平衡跨集群的数据和负载，自动重新分配文档，并将读写操作路由到正确的机器上。

MongoDB包含了索引、聚合、特殊的集合和索引类型、文件存储等功能。

MongoDB在其WiredTiger存储引擎中使用了机会锁，以最大限度地提高并发和吞吐量。它会使用尽可能多的RAM作为缓存，并尝试为查询自动选择正确的索引。




<br/>

---

<br/>




# 入门指南

一些基本概念:

- 文档是基本数据单元，可理解为行。
- 集合，可理解为表。
- 数据库
- 每个文档都有一个特殊的键`_id`，其在所属的集合中是唯一的。
- mongo shell是一个功能强大的javascript解释器。


<br/>
<br/>


## 文档

文档是一组有序键值的集合，不能包含重复的键。


<br/>
<br/>


## 集合

集合就是一组文档。集合具有**动态模式**的特性，这意味着一个集合中的文档可以具有任意数量的不同形状。但创建模式并将相关类型的文档放在一起非常合理。通过将单一类型的文档放入集合，可以更高效地对集合进行索引。

使用子集合来组织数据在很多场景中是一个好方法。


<br/>
<br/>


## 库

数据库对集合进行分组。库名称长度限制为64字节。一个推荐的做法是将单个应用程序的所有数据都存储在一个库里。

将库和集合名称连接起来，可以获得一个完全限定的集合名称，称位**命名空间**。命名空间长度限制为120字节，实际应该小于100字节。

一些库名是保留的，有特殊含义：

- `admin`: 身份认证和授权。
- `local`: 特定于单个服务器的数据会存储在此数据库中。在副本集中，用于存储复制过程中所使用的数据，而local库不会被复制。
- `config`: 分片集群使用它来存储分片的信息。


<br/>
<br/>


## 数据类型

MongoDB中文档可以被认为是类似于JSON的形式。

常见的类型有以下几种:

- null: 空值或不存在的值
- 布尔
- 数值: 默认使用64为浮点数
- 字符串: 任何utf8字符串
- 日期: 将日期存储为64为整数，表示自Unix纪元以来的毫秒数。
- 正则表达式: 与js的正则语法相同
- 数组
- 内嵌文档
- Object ID: 12字节的ID，是文档的唯一标识。`_id`默认为此类型。
- 二进制数据: 如果要将非utf8字符串存入数据库，使用二进制数据是唯一的办法。
- 代码: 任意js代码


<br/>
<br/>


## mongo shell

因为mongo shell就是一个js shell，所以可通过js的在线文档获得大量帮助。可以输入`help`命令进行访问。

可以使用mongo shell执行js脚本。

<br/>

mongo shell辅助函数对应的js函数:

| 辅助函数 | 等价函数 |
| - | - |
| use db1 | db.getSisterDB("db1") |
| show dbs | db.getMongo().getDBs() |
| show collections | db.getCollectionNames() |

<br/>

如果你有一些需要频繁被加载的脚本，那么可将它们添加到`.mongorc.js`文件中。此文件会在启动mongo shell时自动执行。

最常见的用途是移除哪些比较问现的mongo shell命令，调用下面这些命令会打印出错误消息。

```javascript
var no = function() {
	print("Not on my watch.")
}

// 禁止删除数据库
db.dropDatabase = DB.prototype.dropDatabase = no;

// 禁止删除集合
DBCollection.prototype.drop = no;

// 禁止删除单个索引
DBCollection.prototype.dropIndex = no;
// 禁止删除多个索引
DBCollection.prototype.dropIndexes = no;
```




<br/>

---

<br/>




# 增删查改

数据库的 CRUD 操作，详细的操作符请查看参考消息-操作符。

- 不建议使用 `insert()`，应该使用 `insertOne()` 或 `insertMany()` 来创建文档。
- 不建议使用 `remove()`，应该使用 `deleteOne()` 或 `deleteMany()` 来删除文档。
- 使用 `updateOne()` 或 `updateMany()` 更新文档。
- `replaceOne()` 会用新文档完全替换匹配的文档，这对于大规模模式迁移的场景非常有用。
- `upsert()` 是一种特殊类型的更新。如果找不到与筛选条件相匹配的文档，则会以这个条件和更新文档为基础来创建一个新文档；如果找到了匹配的文档，则进行正常的更新。
- 定位运算符 `$` 可以计算出查询文档匹配的数组元素并更新该元素。

<br/>
<br/>

## 插入文档

MongoDB 会对插入的数据进行最基本的检查，检查文档的基本结构，如果不存在 `_id` 字段，则自动添加一个。其中一项最基本的结构检查是文档大小，所有文档都必须小于16MB。可使用 `Object.bsonsize(doc)` 来查看文档的 bson 大小。

为了让你对16MB的数据量有一个概念，以 “《战争与和平》” 为例，整部著作也只有 3.14MB。

```js
// 插入单个文档
db.movies.insertOne({"title": "Stand by Me"})

// 插入多个文档
db.movies.insertMany([{"title" : "Ghostbusters"}, {"title" : "E.T."}, {"title" : "Blade Runner"}]);

// 如果中途某个文档发生了某种错误，那么接下来会发生什么取决于所选择的是有序操作还是无序操作。
// 将 ordered 键指定为 true（默认值），可以确保文档按提供的顺序插入。为 false 则允许重新排列插入的顺序以提高性能。
// 对于有序插入，产生插入错误后，数组中之后的文档不会被插入。对于无序插入，将尝试插入所有文档。
db.movies.insertMany([
  {"_id" : 3, "title" : "Sixteen Candles"},
  {"_id" : 4, "title" : "The Terminator"},
  {"_id" : 4, "title" : "The Princess Bride"},
  {"_id" : 5, "title" : "Scarface"}],
  {"ordered" : false})
```

<br/>
<br/>

## 删除文档

删除数据是永久性的，没有任何方法可以撤回删除文档或者集合的操作，也无法恢复被删除的文档，请特别小心！

```js
// 删除一个文档
// 如果筛选条件匹配多个文档，它将删除满足条件的第一个文档
db.movies.deleteOne({"_id" : 4})

// 删除满足筛选条件的所有文档
 db.mailing.list.deleteMany({"opt-out" : true})
 
 // 删除一个集合中的所有文档
 db.movies.deleteMany({})
 // 如果想清空集合，直接使用 drop 删除集合，然后在这个空集合重建各项索引会更快
 db.movies.drop()
```

<br/>
<br/>

## 更新文档

更新文档是原子操作：如果两个更新同时发生，那么首先到达服务器的更新会先被执行，然后再执行下一个更新。

```js
var joe = db.people.findOne({"name" : "joe", "age" : 20});
joe.age++;
db.people.replaceOne({"_id" : ObjectId("4b2b9f67a1f631733d917a7c")}, joe)


// $inc 增加一个字段的值，只能用于数字（整型、长整型或双精度浮点），其它类型会失败。
db.analytics.updateOne({"url" : "example-domain"}, {"$inc" : {"pageviews" : 1}})
// $inc 在键不存在时创建它
db.games.insertOne({"game" : "pinball", "user" : "joe"})
db.games.updateOne({"game" : "pinball", "user" : "joe"}, {"$inc" : {"score" : 50}})


// $set 设置一个字段的值
 db.users.updateOne({"_id" : ObjectId("4b253b067525f35f94b60a31")}, {"$set" : {"favorite book" : "War and Peace"}})
// $set 还可以修改键的类型
db.users.updateOne({"name" : "joe"},
  {"$set" : {"favorite book" : ["Cat's Cradle", "Foundation Trilogy", "Ender's Game"]}})

// $unset 删除一个字段
 db.users.updateOne({"name" : "joe"}, {"$unset" : {"favorite book" : 1}})


// 操作数组的运算符
// $push 向数组末尾添加元素，如果数组不存在则创建
db.blog.posts.updateOne({"title" : "A blog post"},
  {"$push" : {"comments" :
    {"name" : "joe", "email" : "joe@example.com", "content" : "nice post."}}})

// $each 在一次操作中添加多个值
db.stock.ticker.updateOne({"_id" : "GOOG"},
  {"$push" : {"hourly" : {"$each" : [562.776, 562.790, 559.123]}}})

// $slice 防止数组的增长超过某个大小
db.movies.updateOne({"genre" : "horror"},
  {"$push" : {"top10" : {"$each" : ["Nightmare on Elm Street", "Saw"],
  "$slice" : -10}}})

// $addToSet 避免插入重复的邮件地址
db.users.updateOne({"_id" : ObjectId("4b2d75476cc613d5ee930164")},
  {"$addToSet" : {"emails" : "joe@hotmail.com"}})

// 添加多个不重复的值
db.users.updateOne({"_id" : ObjectId("4b2d75476cc613d5ee930164")},
  {"$addToSet" : {"emails" : {"$each" : ["joe@php.net", "joe@example.com", "joe@python.org"]}}})

// 如果将数组视为队列或栈，$pop 从任意一端删除元素
// {"$pop" : {"key" : 1}} 会从数组末尾删除一个元素，{"$pop" : {"key" : -1}} 则会从头部删除它。

// $pull 删除特定条件的数组元素
db.lists.updateOne({}, {"$pull" : {"todo" : "laundry"}})


// 基于位置的数组更改
db.blog.updateOne({"post" : 20201011}, {"$inc" : {"comments.0.votes" : 1}})
db.blog.updateOne({"comments.author" : "John"}, {"$set" : {"comments.$.author" : "Jim"}})


// 使用数组过滤器进行更新， MongoDB 3.6 引入了 arrayFilters


// $upsert
db.analytics.updateOne({"url" : "/blog"}, {"$inc" : {"pageviews" : 1}}, {"upsert" : true})


// $updateMany 更新多个文档，用法和 $updateOne 一样，只是更新的文档数量不同。
```

<br/>

---

<br/>

# 查询

涵盖以下方面:

- 使用 `$` 条件进行范围查询、数据集合查询、不等式查询和其它查询；
- 查询会返回一个数据库游标，其只会在需要时才惰性地进行批量返回；
- 有很多可以针对游标执行的元操作，包括跳过一定数量的结果、限定返回结果的数量，以及对结果进行排序。

<br/>
<br/>

## find介绍

传递给数据库的查询文档的值必须是常量。

```js
// 空查询条件会匹配集合中的所有内容
db.c.find()

// age 27的所有文档
db.users.find({"age": 27})

// 返回指定的键，剔除不需要的键。_id 键会默认返回
db.users.find({"name": "joe", "age": 27}, {"username": 1, "email": 1, "_id": 0})
```

<br/>
<br/>

## 查询条件

匹配更加复杂的条件。

```js
// 比较运算符: $lt, $lte, $gt, $gte, $ne
db.users.find({"aget": {"$gte": 18, "$lte": 30}})

// 日期范围
start = new Date("01/01/2007")
db.users.find({"registered": {"$lt": start}})

// 不等于
db.users.find({"username": {"$ne": "joe"}})

// $in, $nin, $or, $not, $mod
db.users.find({"user_id": {"$in": [123, 456]}})
db.users.find({"$or": [{"name": {"$in": ["a", "b"]}}, {"winner": true}]})
db.users.find({"id_num": {"$not": {"$mod": [5, 1]}}})
```

<br/>
<br/>

## 特定类型的查询

`null` 的行为有一些特别，它可以与自身匹配，也会匹配不存在这个条件。

```js
// 仅匹配值为null的文档
db.c.find({"z": {"$eq": null, "$exists": true}})
```

<br/>

`$regex` 可在查询中为字符串的模式匹配提供正则表达式功能。MongoDB 使用 Perl 兼容的正则表达式（PCRE）库来匹配。

```js
// 不区分大小写
db.users.find({"name": {"$regex": /joey?/i}})
```

<br/>

查询数组元素的方式与查询标量值相同。

```js
db.food.insertOne({"fruit": ["apple", "banana", "peach"])

// 查询其中一个内容也可以成功匹配到文档
db.food.find({"fruit": "banana"})

// 如果需要通过多个元素来匹配数组，使用 $all
db.food.find({"fruit": {"$all": ["apple", "banana"]}})

// 可以使用数组下标来查询
db.food.find({"fruit.2": "peach"})

// $size 查询特定长度的数组
db.food.find({"fruit": {"$size": 3}})

// $slice 返回数组中特定子集，返回后 10 条评论
db.blog.posts.findOne(criteria, {"comments": {"$slice": -10}})
db.blog.posts.findOne(criteria, {"comments": {"$slice": [23, 10]}})

// 返回查询条件匹配的任意数组元素
db.blog.posts.find({"comments.name": "Bob"}, {"comments.$": 1})

// 要正确指定一组条件而无须指定每个键，请使用`$elemMatch`。
db.blog.find({"comments": {"$eleMatch": ... {"author": "joe", "score": {"$gte": 5}}}})
```

<br/>
<br/>

## $where查询

`$where` 子句允许在查询中执行任意的 js 代码。为了安全起见，应该严格限制或消除 `$where` 的使用，应该禁止终端用户随意使用 `$where` 子句。

```js
db.foo.find({"$where": function () {
  for (var current in this) {
    for (var other in this) {
      if (current != other && this[current] == this[other]) {
        rturn true;
      }
    }
  }
  return false;
}});
```

除非必要，否则不应该使用 `$where` 查询：它们比常规查询慢得多。每个文档都必须从 BSON 转化为 js 对象。此外它也无法使用索引。

<br/>
<br/>

## 游标

数据库会使用游标返回 `find` 的执行结果。游标的客户端实现通常能够在很大程度上对查询的最终输出进行控制。

<br/>

最常用的查询选项是限制、略过以及排序(sort)。所有这些选项必须在查询被发送到数据库之前指定。

```js
db.c.find().limit(3)

db.c.find().skip(3)

// 1升序， -1降序
db.c.find().sort({"username": 1, "age": -1})
```

<br/>

略过大量的结果会导致性能问题。因为需要找到被略过的结果，然后再丢弃这些数据。应该避免略过大量的数据。

<br/>

游标包括两个部分：面向客户端的游标，和由客户端游标所表示的数据库游标。

在服务器端，游标会占用内存和资源，一旦游标遍历完结果之后，或者客户端发送一条消息要求终止，数据库就可以释放它正在使用的资源。释放这些资源可以让数据库将其用于其他用途，这是非常有益的，因此要确保可以尽快释放游标。

如果10分钟没有被使用的话，数据库游标也将自动销毁。

有时可能需要一个游标维持很长时间。这种情况下，许多驱动程序实现了一个称为 `immportal` 的函数，或者类似的机制。它告诉数据库不要让游标超时。如果关闭了游标超时，则必须遍历完所有结果或主动将其销毁以确保游标被关闭。否则，它会一直占用数据库的资源，知道服务器重新启动。

<br/>

---

<br/>

# 索引

索引使你能够高效地执行查询。为集合选择正确的索引对性能至关重要。

<br/>
<br/>

## 索引简介

数据库索引类似于图书索引。有了索引便不需要浏览整本书，而是可以采取一种快捷方式，只查看一个有内容引用的有序列表。这使得 MongoDB 的查找速度提高了好几个数量级。

不使用索引的查询称为 **集合扫描**（全表扫描），这意味着服务器端必须 “浏览整本书” 才能得到查询的结果。

一个示例，创建一个包含百万文档的集合:

```js
for (i=0; i<1000000; 1++) {
	db.users.insertOne(
    	{
        	"i": i,
            "username": "user" + i,
            "age": Math.floor(Math.random()*120),
            "created": new Date()
        }
    );
}
```

通过不使用索引和使用索引，研究这个集合中查询的性能差异。

<br/>

可以使用 `explain` 命令来查看 MongoDB 在执行查询时所需要做的事情。此游标方法提供了 CRUD 操作执行的各种信息。使用 executionStats 模式有助于理解使用索引进行查询的效果。

```js
db.users.find({"username": "user101"}).explain("executionStats")
{
 "queryPlanner" : {
   "plannerVersion" : 1,
   "namespace" : "test.users",
   "indexFilterSet" : false,
   "parsedQuery" : {
     "username" : {
       "$eq" : "user101"
     }
   },
   "winningPlan" : {
     "stage" : "COLLSCAN",
     "filter" : {
       "username" : {
         "$eq" : "user101"
       }
    },
    "direction" : "forward"
    },
   "rejectedPlans" : [ ]
 },
 "executionStats" : {
   "executionSuccess" : true,
   "nReturned" : 1,
   "executionTimeMillis" : 419,
   "totalKeysExamined" : 0,
   "totalDocsExamined" : 1000000,
   "executionStages" : {
     "stage" : "COLLSCAN",
     "filter" : {
       "username" : {
         "$eq" : "user101"
       }
     },
     "nReturned" : 1,
     "executionTimeMillisEstimate" : 375,
     "works" : 1000002,
     "advanced" : 1,
     "needTime" : 1000000,
     "needYield" : 0,
     "saveState" : 7822,
     "restoreState" : 7822,
     "isEOF" : 1,
     "invalidates" : 0,
     "direction" : "forward",
     "docsExamined" : 1000000
     }
   },
   "ok" : 1
}
```

<br/>

介绍下 `executionStats` 字段下的内嵌文档。

`totalDocsExamined`是 MongoDB 在试图查询时查看的文档总数。可以看到，集合中的每个文档都被扫描过了。也就是说，MongoDB 必须查看每个文档中的每个字段。

`executionTimeMills` 字段显示了执行查询所用的毫秒数。

`nReturned`字段显示返回的结果数是 1，因为只有一个用户名为 "user101" 的用户。注意，MongoDB 必须在集合的每个文档中查找匹配项，因为它不知道用户名是唯一的。

为了使 MongoDB 高效地响应查询，应用程序中的所有查询模式（指应用程序向数据库提出的不同类型的问题）都应该有索引支持。

<br/>
<br/>

### 创建索引

在 `username` 字段上创建索引。

```js
db.users.createIndex({"username": 1})

// 建索引过程会阻塞其它数据库操作，background可指定以后台方式创建索引
db.users.createIndex({"username": 1}, {"background": true})

// 查看索引
db.users.getIndexes()
```

索引的创建时间和集合大小有关。可以使用 `db.currentOp()` 查看索引创建进度。

<br/>

有了索引之后，再次执行之前的查询。

```js
db.users.find({"username": "user101"}).explain("executionStats")

{
		...
        "executionStats" : {
                "executionSuccess" : true,
                "nReturned" : 1,
                "executionTimeMillis" : 1,
                "totalKeysExamined" : 1,
                "totalDocsExamined" : 1,
                "executionStages" : {
                        "stage" : "FETCH",
                        "nReturned" : 1,
                        "executionTimeMillisEstimate" : 0,
                        "works" : 2,
                        "advanced" : 1,
                        "needTime" : 0,
                        "needYield" : 0,
                        "saveState" : 0,
                        "restoreState" : 0,
                        "isEOF" : 1,
                        "invalidates" : 0,
                        "docsExamined" : 1,
                        "alreadyHasObj" : 0,
                        "inputStage" : {
                                "stage" : "IXSCAN",
                                "nReturned" : 1,
                                "executionTimeMillisEstimate" : 0,
                                "works" : 2,
                                "advanced" : 1,
                                "needTime" : 0,
                                "needYield" : 0,
                                "saveState" : 0,
                                "restoreState" : 0,
                                "isEOF" : 1,
                                "invalidates" : 0,
                                "keyPattern" : {
                                        "username" : 1
                                },
                                "indexName" : "username_1",
                                "isMultiKey" : false,
                                "multiKeyPaths" : {
                                        "username" : [ ]
                                },
                                "isUnique" : false,
                                "isSparse" : false,
                                "isPartial" : false,
                                "indexVersion" : 2,
                                "direction" : "forward",
                                "indexBounds" : {
                                        "username" : [
                                                "[\"user101\", \"user101\"]"
                                        ]
                                },
                                "keysExamined" : 1,
                                "seeks" : 1,
                                "dupsTested" : 0,
                                "dupsDropped" : 0,
                                "seenInvalidated" : 0
                        }
                }
        },
    	...
}
```

可以看到，查询几乎是一瞬间完成的。而且查询任何用户名所花费的时间基本上是一致的。

<br/>

索引可以显著缩短查询时间。然而，使用索引是有代价的：修改索引字段的写操作（插入、更新和删除）会花费更长的时间。这是因为在更改数据时，除了更新字段，还必须更新索引。通常来说，这个代价是值得的。关键是要找出要索引的字段。

MongoDB 索引的工作原理与典型的关系型数据库索引几乎相同。

要选择为哪些字段创建索引，可以查看常用的查询以及那些需要快速执行的查询，并尝试从中找到一组通用的键。然而，如果一个很少用到的查询或是由管理员执行的不太关心时间消耗的查询，那么就不应该在此上面创建索引。

<br/>
<br/>

### 复合索引

索引的目的是使查询尽可能高效。对于许多查询模式来说，在两个或更多的键上创建索引是必要的。这称为 **复合索引**（compound index）。如果查询中有多个排序方向或者查询条件中有多个键，复合索引会很有用。复合索引是创建在多个字段上的索引。

例如，索引会将其所有值按顺序保存，因此按照索引键对文档进行排序的速度要快得多。然而，索引只有在作为排序的前缀时才有助于排序。

```js
// username 上的索引对这个排序就没有帮助
// 这里先根据 age，再根据 username 进行排序，所以严格按照 username 排序并没有什么帮助。
db.users.find().sort({"age": 1, "username": 1})

// 要优化这种排序，可在 age 和 username 上创建复合索引
db.users.createIndex({"age": 1, "username": 1}, {"background": true})
```

<br/>

假设有如下所示一个 users 集合，并且执行不带排序（称为自然顺序）的查询。

```js
db.users.find({}, {"_id" : 0, "i" : 0, "created" : 0})
{ "username" : "user0", "age" : 69 }
{ "username" : "user1", "age" : 50 }
{ "username" : "user2", "age" : 88 }
{ "username" : "user3", "age" : 52 }
{ "username" : "user4", "age" : 74 }
{ "username" : "user5", "age" : 104 }
...
```

如果使用 `{"age": 1, "username": 1}` 在集合中创建索引，那么这个索引会是如下这样：

```js
[0, "user100010"] -> 8623513776
[0, "user1002"] -> 8599246768
...
[0, "user100414"] -> 8623564208
[1, "user100113"] -> 8623525680
...
[1, "user100626"] -> 8623591344
[2, "user100191"] -> 8623535664
...
```

每个索引项都包含了年龄和用户名，并指向一个记录标识符。存储引擎在内部使用记录标识符来定位文档数据。age 和 username 按升序排列。

<br/>

MongoDB 使用该索引的方式取决于所执行的查询类型。以下是 3 种最常见的方式。

- 等值查询
- 范围查询
- 多值查询

<br/>
<br/>

#### 等值查询

等值查询，用于查找单个值。可能有多个文档具有该值。

```js
// 等值查询，用于查找单个值
db.users.find({"age": 21}).sort({"username": -1})

// 多亏了索引的第二个字段，结果已经按照正确的顺序排序
// MongoDB 可从 {"age": 21} 的最后一个匹配项开始，然后依次遍历索引
[21, "user100154"] -> 8623530928
[21, "user100266"] -> 8623545264
[21, "user100270"] -> 8623545776
...
```

这种类型的查询非常高效：MongoDB 可以直接跳转到正确的年龄，并且不需要对结果进行排序，因为只要遍历索引就会以正确的顺序返回数据。

注意，排序方向并不重要，MongoDB 可以在任意方向上遍历索引。

<br/>
<br/>

#### 范围查询

范围查询，用于查找与多个值相匹配的文档。

```js
db.users.find({"age" : {"$gte" : 21, "$lte" : 30}})

// MongoDB 会使用索引中的第一个键 age，以返回匹配的文档
[21, "user100154"] -> 8623530928
...
[22, "user100017"] -> 8623513392
...
```

通常来说，如果 MongoDB 使用索引进行查询，那么它会按照索引顺序返回结果文档。

<br/>
<br/>

#### 多值查询

```js
db.users.find({"age": {"$gte": 21, "$lte": 30}}).sort({"username" : 1})
```

MongoDB 使用索引来匹配查询条件。不过，索引不会按照顺序返回用户名，而查询要求按用户名对结果进行排序。这意味着 MongoDB 需要在返回结果前在内存中对结果进行排序，而不是简单地遍历已经按需排好序的索引。因此，这种类型的查询通常效率较低。

当然，速度取决于有多少结果与查询条件相匹配。如果结果集中只有几个文档，那么 MongoDB 将不会耗费多少时间进行排序。如果结果非常多，那么速度会很慢或根本不能工作。如果结果超过了 32MB，MongoDB 就会报错，拒绝对这么多数据进行排序。

```log
Error: error: {
 "ok" : 0,
 "errmsg" : "Executor error during find command: OperationFailed:
Sort operation used more than the maximum 33554432 bytes of RAM. Add
an index, or specify a smaller limit.",
 "code" : 96,
 "codeName" : "OperationFailed"
}
```

如果要避免这个问题，则必须创建一个支持此排序操作的索引，或将 `limit` 与 `sort` 结合使用以使结果低于 32MB。

在设计复合索引时，将排序键放在第一位通常是一个好策略。这是在考虑如何兼顾等值查询、多值查询以及排序来构造复合索引时的最佳实践之一。

<br/>
<br/>

### 如何选择索引

MongoDB 是如何选择索引来满足查询。假设有 5个索引。当有查询进来时，MongoDB 会查看这个查询的 **形状**。这个形状与要搜索的字段和一些附加信息（比如是否有排序）有关。基于这些信息，系统会识别出一组可能用于满足查询的候选索引。

假设有一个查询进入，5个索引中的 3个被标识为该查询的候选索引。然后，MongoDB 会创建 3个查询计划，分别为每个索引创建 1个，并在 3个并行线程中运行此查询，每个线程使用不同的索引。这样做的目的是看哪一个能够最快地返回结果。

到达目标状态的第一个查询计划成为赢家。更重要的是，以后会选择它作为索引，用于具有相同形状的其他查询。每个计划会相互竞争一段时间（称为试用期），之后每一次的结果都会用来在总体上计算出一个获胜的计划。

要赢得计划，查询线程必须首先返回所有查询结果或按排序顺序返回一些结果。考虑到在内存中执行排序的开销，其中排序的部分非常重要。

让多个查询计划相互竞争的真正价值在于，对于具有相同形状的后续查询，MongoDB 会知道要选择哪个索引。服务器端维护了查询计划的缓存。一个获胜的计划存储在缓存中，以备在将来用于进行该形状的查询。

随着时间的推移以及集合和索引的变化，查询计划可能会从缓存中被淘汰。而 MongoDB 会再次进行尝试，以找到最适合当前集合和索引集的查询计划。其他会导致计划从缓存中被淘汰的事件有：重建特定的索引、添加或删除索引，显式清除计划缓存。重启 mongod 进程也会导致查询计划缓存丢失。

![MongoDB如何选择索引](https://raw.githubusercontent.com/zhang21/images/master/cs/databases/mongodb/5-1.png)

<br/>
<br/>

### 使用复合索引

复合索引要比单键索引要复杂一些，但也更强大。设计复合索引时需要进行各种思考，目标是使读写操作尽可能高效。

首先需要考虑的是索引的选择性。对于给定的查询模式，索引将在多大程度上减少扫描的记录数。

<br/>

示例，包含一百万条记录的学生数据集，此数据集中的文档就像下面这样:

```json
{
 "_id" : ObjectId("585d817db4743f74e2da067c"),
 "student_id" : 0,
 "scores" : [
 {
 "type" : "exam",
 "score" : 38.05000060199827
 },
 {
 "type" : "quiz",
 "score" : 79.45079445008987
 },
 {
 "type" : "homework",
 "score" : 74.50150548699534
 },
 {
 "type" : "homework",
 "score" : 74.68381684615845
 }
 ],
 "class_id" : 127
}
```

<br/>

我们将从两个索引开始，看看 MongoDB 如何使用（或不使用）这些索引来满足查询。

```js
db.students.createIndex({"class_id": 1})
db.students.createIndex({"student_id": 1, class_id: 1})
```

会围绕以下查询，因为这个查询可以说明在设计索引时必须考虑的几个问题。

```js
db.students.find({student_id: {$gt: 500000}, class_id: 54})
  .sort({student_id: 1})
  .explain("executionStats")
```

此查询对 `student_id` 大于五十万的所有记录进行了请求，这会有一半的记录。我们还将搜索限制在了 `class_id` 为 54 的记录中。最后，根据 `student_id` 按升序进行排序。

通过查看 explain 方法提供的执行统计信息，来说明 MongoDB 如何使用索引来完成这个查询。

```js
// 为了定位 9903 个匹配文档，一共检查了 850 477 个索引键。
// 这表示完成此查询的索引选择性比较低。运行时间超过了 4.3 秒。
{
  ...
  "executionStats": {
    "executionSuccess": true,
    "nReturned": 9903,
    "executionTimeMillis": 4325,
    "totalKeysExamined": 850477,
    "totalDocsExamined": 9903,
    "executionStages": {
    }
  },
  ...
}
```

输出的前面部分是获胜的查询计划（winningPlan 字段）。查询计划描述了 MongoDB 用来满足查询的步骤。我们尤其对使用了哪个索引以及 MongoDB 必须在内存中进行排序感兴趣。

```js
"winningPlan": {
  "stage": "FETCH",
  "inputStage": {
  	"stage": "IXSCAN",
    "keyPattern": {
      "student_id": 1,
      "class_id": 1
    },
    "indexName": "student_id_1_class_id_1",
    ...
  },
  ...
}
```

expalin 的输出查询计划显示为一颗包含各个阶段的树。一个阶段可以有一个或多个输入阶段，这取决于它有多少个子阶段。输入阶段向其父阶段提供文档或索引键。本例中有一个输入阶段，即索引扫描。该扫描阶段向其父阶段 “FETCH” 提供了哪些匹配查询的文档的记录 ID。然后，FETCH 阶段会获取文档本身，并将其分批返回给发出请求的客户端。

失败的查询计划（本例只有一个）则会使用基于 `class_id` 的索引，但之后它必须进行内存排序。当在查询计划中看到 SORT 阶段时，意味着 MongoDB 将无法使用索引对数据库中的结果集进行排序，而必须执行内存排序。

```js
"rejectedPlans": [
  {
    "stage": "SORT",
    "sortPattern": {
      "student_id": 1
    },
  }
]
```

对于这个查询，获胜的索引是能够返回排序输出的索引。要获胜，只需要获取测试数量的已排序结果文档。而对于另一个计划，这个查询线程必须要返回整个结果集（将近十万个个文档），因为需要将这些结果在内存中进行排序。

<br/>

上面运行的这个查询，同时包含了多值部分和等值部分。等值部分要求所有记录中 "class_id" 等于 54。这个数据集中只有大约 500 个班级，虽然这些班级中有大量学生，但 "class_id" 在执行此查询时更具选择性。这是这个值将结果集限制在了十万条以下，而不是多值部分所定位到的八十五万多条。

换句话说，在当前情况下，如果可以使用基于 `calss_id` 的索引（失败查询计划中的索引）则会比较好。MongoDB 提供了两种强制数据库使用特定索引的方法。不过，使用这些方法来覆盖查询计划器的结果时应该非常谨慎，也不应该在生成环境中使用。

<br/>

游标的 `hint` 方法能够通过索引的形状或名称来指定要使用的特定索引。

如下所示，如果使用 hint 稍微更改以下查询，那么 explain 的输出结果会完全不同。

```js
db.students.find({student_id: {$gt: 500000}, class_id: 54})
  .sort({{student_id: 1}})
  .hint({class_id: 1})
  .explain("executionStats")

// 扫描了两万个索引键，执行时间为 272 毫秒
"executionStats": [
  "nReturned": 9903,
  "executionTimeMillis": 272,
  "totalKeysExamined": 20076,
  "totalDocsExamined": 20076,
  ...
]
```

然而，我们真正希望看到的是 nReturned 与 totalKeysExamined 非常接近。此外，为了更有效地执行此查询，我们希望可以不使用 hint。解决这两个问题的方法是设计一个更好的索引。

对于这里所讨论的查询模式，更好的索引应该基于 "class_id" 和 "student_id"，两个键的顺序不能变。以 "class_id" 作为前缀，在查询中使用等值过滤来限制索引需要考虑的键。这是查询中最具选择性的部分，从而有效限制了 MongoDB 完成此查询所需考虑的键的数量。

```js
db.students.createIndex({class_id: 1, student_id: 1})
```

虽然不是所有数据集都这样，但通常在设计复合索引时，应将等值过滤字段排在多值过滤字段之前。

有了新索引之后，重新执行查询时就不需要提示了。可从 explain 的输出结果中的 executionStats 字段看到，查询速度非常快（37毫秒），其中返回的结果数（nReturned）等于索引所扫描的键数（totalKeysExamined）。还可以看到，这个结果是因为 executionStages 所对应的获胜的查询计划包含了一个索引扫描，它使用了新创建的索引。

```js
...
"executionStats": {
 "executionSuccess": true,
 "nReturned": 9903,
 "executionTimeMillis": 37,
 "totalKeysExamined": 9903,
 "totalDocsExamined": 9903,
 "executionStages": {
 "stage": "FETCH",
 "nReturned": 9903,
 "executionTimeMillisEstimate": 36,
 "works": 9904,
 "advanced": 9903,
 "needTime": 0,
 "needYield": 0,
 "saveState": 81,
 "restoreState": 81,
 "isEOF": 1,
 "invalidates": 0,
 "docsExamined": 9903,
 "alreadyHasObj": 0,
 "inputStage": {
 "stage": "IXSCAN",
 "nReturned": 9903,
 "executionTimeMillisEstimate": 0,
 "works": 9904,
 "advanced": 9903,
 "needTime": 0,
 "needYield": 0,
 "saveState": 81,
 "restoreState": 81,
 "isEOF": 1,
 "invalidates": 0,
 "keyPattern": {
 "class_id": 1,
 "student_id": 1
 },
 "indexName": "class_id_1_student_id_1",
 "isMultiKey": false,
 "multiKeyPaths": {
 "class_id": [ ],
 "student_id": [ ]
 },
 "isUnique": false,
 "isSparse": false,
 "isPartial": false,
 "indexVersion": 2,
 "direction": "forward",
 "indexBounds": {
 "class_id": [
 "[54.0, 54.0]"
 ],
 "student_id": [
 "(500000.0, inf.0]"
 ]
 },
 "keysExamined": 9903,
 "seeks": 1,
 "dupsTested": 0,
 "dupsDropped": 0,
 "seenInvalidated": 0
 }
 }
},
```

<br/>

如果思考一下创建索引的原理，就能明白为什么会有这样的结果。"[class_id, student_id]" 索引由如下一对对键组成。由于学生 ID 在其中是有序的，因此为了满足排序要求，MongoDB只需从 class_id 为 54 的第一对键开始全部进行遍历。

```js
...
[53, 999617]
[53, 999916]
[54, 500001]
[54, 500048]
...
```

在考虑复合索引的设计时，需要知道对于利用索引的通用查询模式，如何处理其 **等值过滤**、**多值过滤** 以及 **排序** 这些部分。对于所有复合索引都必须考虑这 3个因素，而且如果在设计索引时可以正确地平衡这些关注点，那么你的查询就会从 MongoDB 中获得最佳的性能。

<br/>

虽然 "[class_id, student_id]" 索引已经处理了全部 3 个要素，但要进行排序的字段同样是其中一个需要过滤的字段，而这样的查询是复合索引问题的一种特殊情况。

为了消除这个特殊情况，我们改为按照成绩进行排序，更改后的查询如下。

```js
db.statudents.find({student_id: {$gt: 500000}, class_id: 54})
.sort({final_grade: 1})
.explain("executionStats")
```

运行这个查询并查看 explain 输出，就会发现这里使用了内存排序。虽然查询速度仍然很快（136ms），但由于使用了内存排序，因此比在 student_id 上排序慢了一个数量级。可以看到，在进行内存排序时，获胜的查询计划包含一个 "SORT" 阶段。

```js
...
"executionStats": {
 "executionSuccess": true,
 "nReturned": 9903,
 "executionTimeMillis": 136,
 "totalKeysExamined": 9903,
 "totalDocsExamined": 9903,
 "executionStages": {
 "stage": "SORT",
 "nReturned": 9903,
 "executionTimeMillisEstimate": 36,
 "works": 19809,
 "advanced": 9903,
 "needTime": 9905,
 "needYield": 0,
 "saveState": 315,
 "restoreState": 315,
 "isEOF": 1,
 "invalidates": 0,
 "sortPattern": {
 "final_grade": 1
 },
 "memUsage": 2386623,
 "memLimit": 33554432,
 "inputStage": {
 "stage": "SORT_KEY_GENERATOR",
 "nReturned": 9903,
 "executionTimeMillisEstimate": 24,
 "works": 9905,
 "advanced": 9903,
 "needTime": 1,
 "needYield": 0,
 "saveState": 315,
 "restoreState": 315,
 "isEOF": 1,
 "invalidates": 0,
 "inputStage": {
 "stage": "FETCH",
 "nReturned": 9903,
 "executionTimeMillisEstimate": 24,
 "works": 9904,
 "advanced": 9903,
 "needTime": 0,
 "needYield": 0,
 "saveState": 315,
 "restoreState": 315,
 "isEOF": 1,
 "invalidates": 0,
 "docsExamined": 9903,
 "alreadyHasObj": 0,
 "inputStage": {
 "stage": "IXSCAN",
 "nReturned": 9903,
 "executionTimeMillisEstimate": 12,
 "works": 9904,
 "advanced": 9903,
 "needTime": 0,
 "needYield": 0,
 "saveState": 315,
 "restoreState": 315,
 "isEOF": 1,
 "invalidates": 0,
 "keyPattern": {
 "class_id": 1,
 "student_id": 1
 },
 "indexName": "class_id_1_student_id_1",
 "isMultiKey": false,
 "multiKeyPaths": {
 "class_id": [ ],
 "student_id": [ ]
 },
 "isUnique": false,
 "isSparse": false,
 "isPartial": false,
 "indexVersion": 2,
 "direction": "forward",
 "indexBounds": {
 "class_id": [
 "[54.0, 54.0]"
 ],
 "student_id": [
 "(500000.0, inf.0]"
 ]
 },
 "keysExamined": 9903,
 "seeks": 1,
 "dupsTested": 0,
 "dupsDropped": 0,
 "seenInvalidated": 0
 }
 }
 }
 }
},
...
```

<br/>

如果可以的话，应该用更好的索引设计来避免内存排序。这样便能从数据集大小和系统负载两个方面更容易地进行扩展。

但要做到这一点，必须做出权衡。这在设计复合索引时是很常见的情况。

为了避免内存排序，需要检查比返回的文档数量更多的键，这对于复合索引来说往往是必需的。为了使用索引进行排序，MongoDB 应该能够按顺序遍历索引键。这意味着需要在复合索引键中包含排序字段。

<br/>

新的复合索引中的键应该按照如下顺序排列："[class_id, final_grade, student_id]"。注意，我们在等值过滤之后立即包含了排序部分，但这是在多值过滤之前。这个索引在缩小此查询所涉及的键的集合时具有非常多的选择性。之后，通过遍历与等值过滤匹配的那些索引，MongoDB 可以识别出与多值过滤部分匹配的记录。并且这些记录将正确地按照最终成绩升序排序。

这个复合索引会迫使 MongoDB 检查比结果集中文档数量更多的键。不过，通过使用索引来确保对文档排序的方式节省了执行时间。

```js
db.students.createIndex({class_id:1, final_grade:1, student_id:1})

db.students.find({student_id: {$gt: 500000}, class_id: 54})
.sort({final_grade: 1})
.explain("executionStats")
```

在输出中查看 executionStats，具体细节和硬件以及系统的其它因素有关，但可以看到获胜的计划中不再包含内存排序，而是使用刚刚创建的索引来满足查询，其中也包括了排序的部分。

```js
"executionStats": {
 "executionSuccess": true,
 "nReturned": 9903,
 "executionTimeMillis": 42,
 "totalKeysExamined": 9905,
 "totalDocsExamined": 9903,
 "executionStages": {
 "stage": "FETCH",
 "nReturned": 9903,
 "executionTimeMillisEstimate": 34,
 "works": 9905,
 "advanced": 9903,
 "needTime": 1,
 "needYield": 0,
 "saveState": 82,
 "restoreState": 82,
 "isEOF": 1,
 "invalidates": 0,
 "docsExamined": 9903,
 "alreadyHasObj": 0,
 "inputStage": {
 "stage": "IXSCAN",
 "nReturned": 9903,
 "executionTimeMillisEstimate": 24,
 "works": 9905,
 "advanced": 9903,
 "needTime": 1,
 "needYield": 0,
 "saveState": 82,
 "restoreState": 82,
 "isEOF": 1,
 "invalidates": 0,
 "keyPattern": {
 "class_id": 1,
 "final_grade": 1,
 "student_id": 1
 },
 "indexName": "class_id_1_final_grade_1_student_id_1",
 "isMultiKey": false,
 "multiKeyPaths": {
 "class_id": [ ],
 "final_grade": [ ],
 "student_id": [ ]
 },
 "isUnique": false,
 "isSparse": false,
 "isPartial": false,
 "indexVersion": 2,
 "direction": "forward",
 "indexBounds": {
 "class_id": [
 "[54.0, 54.0]"
 ],
 "final_grade": [
 "[MinKey, MaxKey]"
 ],
 "student_id": [
 "(500000.0, inf.0]"
 ]
 },
 "keysExamined": 9905,
 "seeks": 2,
 "dupsTested": 0,
 "dupsDropped": 0,
 "seenInvalidated": 0
 }
 }
},
```

<br/>
<br/>

### 复合索引的最佳实践

概括来说，在设计复合索引时：

- 等值过滤的键应该在最前面
- 用于排序的键应该在多值字段之前
- 多值过滤的键应该在最后面
- 复合索引字段顺序类似于：等值字段、排序字段，多值字段（范围字段）

在设计复合索引时应遵循这些准则，然后在实际的工作负载下进行测试，这样就可以确定索引所支持的查询模式都有哪些。

<br/>
<br/>

#### 选择键的方向

注意，相互反转的索引是等价的，如 `{"age" 1, "username": -1}` 使用的查询与 `{"age": -1, "username": 1}` 完全一样。

只有基于多个查询条件进行排序时，索引方向才是重要的。如果只是基于一个键进行排序，那么 MongoDB 可以简单地从反方向读取索引。

如果有一个在 `{"age": -1}` 上的排序和基于 `{"age": 1}` 的索引，那么 MongoDB 会在使用索引时进行优化，就如同存在一个 `{"age": -1}` 索引一样。

<br/>
<br/>

#### 使用覆盖查询

在上面的例子中，索引都是用来查找正确的文档，然后跟随指针去获取实际的文档。然而，如果查询只需要查找索引中包含的字段，那么就没必要去获取实际的文档。

当一个索引包含用户请求的所有字段时，这个索引就 **覆盖** 了本次查询。只要切实可行，就应该优先使用覆盖查询，而不是去获取实际的文档，这样可以使工作集大幅减少。

为了确保查询只使用索引就可以完成，应该使用 **投射**（只返回查询中指定的字段）来避免返回 `_id` 字段（除非它是索引的一部分）。可能还需要对不做查询的字段就行索引，因此在编写的时候就要在所需的查询速度和这种方式带来的开销之间做好权衡。

如果对一个被覆盖的查询运行 explain，那么结果中会有一个并不处于 FETCH 阶段之下的 IXSCAN 阶段，并且在 executionStats 中，totalDocsExamined 的值是 0。

```js
// 比如 username 是索引的一个字段，查询只请求返回 username 字段，这个就是覆盖查询了。
// _id 字段会默认返回
db.users.find({"username": "user1010"}, {"_id": 0, "username": 1})
```

<br/>
<br/>

#### 隐式索引

复合索引具有双重功能，而且针对不同的查询可以充当不同的索引。

如果有一个在 `{"age": 1, "username": 1}` 上的索引，那么 `age` 字段的排序方式就和在 `{age: 1}` 上的索引相同。因此，这个复合索引就可以当作 `{age: 1}` 索引一样使用。

这可以推广到所需的任意多个键：如果一个拥有 N个键的索引，那么你同时免费得到了所有这些键的前缀所组成的索引。如果有一个类似 `{a: 1, b: 1, c: 1, ..., z: 1}` 的索引，那么实际上也等于有了 `{a: 1}`, `{a: 1, b: 1}`, `{a: 1, b: 1, c: 1}` 等一系列左前缀索引。

注意，这并不适用与这些键的任意子集。如 `{b: 1}`, `{a: 1, c: 1}` 作为索引的查询是不是被优化的。只有能够使用索引左前缀的查询才能从中受益。

<br/>
<br/>

### $运算符如何使用索引

有些查询可以比其他查询更高效地使用索引，有些查询则根本不能使用索引。

<br/>
<br/>

#### 低效的运算符

通常来说，取反的效率是比较低的。`$ne` 查询可以使用索引，但不是很有效。由于必须查看所有索引项，而不只是 `$ne` 指定的索引项，因此基本上必须扫描整个索引。

`$not` 有时能够使用索引，但通常它并不知道要如何使用。它可以对基本的范围和正在表达式进行反转。然而，大多数使用 `$not` 的查询会退化为全表扫描。而 `$nin` 总是使用全表扫描。

如果需要快速地执行这些类型的查询，可以尝试是否能找到另一个使用索引的语句，将其添加到查询中。这样就可以在 MongoDB 进行无索引匹配时先将结果集的文档数量减少到一个比较小的数量。

<br/>
<br/>

#### 范围

当设计基于多个字段的索引时，应该将用于精确匹配的字段放在最前面，将范围字段放在最后面。这样可以使查询先用第一个索引键进行精确匹配，然后再用第二个索引范围在这个结果集内部进行搜索。

<br/>
<br/>

#### OR查询

MongoDB 在一次查询中仅能使用一个索引。如在 `{"x": 1}` 上有一个索引，在 `{"y": 1}` 上有另一个索引，然后在 `{"x": 123, "y": 456}` 上进行查询时，MongoDB 只会使用其中一个索引，而不是两个一起使用。唯一的例外是 `$or`，每个 `$or` 子句都可以使用一个索引，因为实际上 `$or` 是执行两次查询后将结果集合并。

```js
db.foo.find({"$or" : [{"x" : 123}, {"y" : 456}]}).explain()

{
 "queryPlanner" : {
 "plannerVersion" : 1,
 "namespace" : "foo.foo",
 "indexFilterSet" : false,
 "parsedQuery" : {
 "$or" : [
 {
 "x" : {
 "$eq" : 123
 }
 },
 {
 "y" : {
 "$eq" : 456
 }
 }
 ]
 },
 "winningPlan" : {
 "stage" : "SUBPLAN",
 "inputStage" : {
 "stage" : "FETCH",
 "inputStage" : {
 "stage" : "OR",
 "inputStages" : [
 {
 "stage" : "IXSCAN",
 "keyPattern" : {
 "x" : 1
 },
 "indexName" : "x_1",
 "isMultiKey" : false,
 "multiKeyPaths" : {
 "x" : [ ]
 },
 "isUnique" : false,
 "isSparse" : false,
 "isPartial" : false,
 "indexVersion" : 2,
 "direction" : "forward",
 "indexBounds" : {
 "x" : [
 "[123.0, 123.0]"
 ]
 }
 },
 {
 "stage" : "IXSCAN",
 "keyPattern" : {
 "y" : 1
 },
 "indexName" : "y_1",
 "isMultiKey" : false,
 "multiKeyPaths" : {
 "y" : [ ]
 },
 "isUnique" : false,
 "isSparse" : false,
 "isPartial" : false,
 "indexVersion" : 2,
 "direction" : "forward",
 "indexBounds" : {
 "y" : [
 "[456.0, 456.0]"
 ]
 }
 }
 ]
 }
 }
 },
 "rejectedPlans" : [ ]
 },
 "serverInfo" : {
 ...,
 },
 "ok" : 1
}
```

可以看到，这里的 explain 需要发你别对两个索引进行两次单独的查询。通常来说，执行两次查询再将结果合并的效率不如单次查询高，因此应该尽可能使用 `$in` 而不是 `$or`。

如果不得不使用 `$or`，则要记住 MongoDB 需要检查两次查询的结果集并从中移除重复的文档。

除非使用排序，否则在用 `$in` 查询时无法控制返回文档的顺序。如 `{"x": {"$in": [1, 2, 3]}}` 与 `{"x": {"$in": [3, 2, 1}}` 返回的文档顺序是相同的。

<br/>
<br/>

### 索引对象和数组

MongoDB 允许深入文档内部，对内嵌字段和数组创建索引。内嵌对象和数组字段可以和顶级字段一起在复合索引中使用。

<br/>
<br/>

#### 索引内嵌文档

示例内嵌文档：

```js
{
 "username" : "sid",
 "loc" : {
 "ip" : "1.2.3.4",
 "city" : "Springfield",
 "state" : "NY"
 }
}
```

可在 "loc" 的其中一个子字段上创建索引，以提高这个字段的查询速度。

```js
// 可用这种方式创建任意深层次的字段 （如 "x.y.z.w.a.b.c"）创建索引
db.users.createIndex({"loc.city" : 1})
```

注意，对内嵌文档本身（如 "loc"）创建索引的行为与对内嵌文档的某个字段（如 "loc.city"）创建索引的行为非常不同。对整个文档创建索引只会提高对整个文档进行查询的速度。只有在进行与子文档字段顺序完全匹配的查询时，查询优化其才能使用 "loc" 上的索引。

<br/>
<br/>

#### 索引数组

也可以对数组创建索引，这样就能高效地查找特定数组元素。

```js
// 如一个博客文章的评论数组字段
db.blog.createIndex({"comments.date" : 1})
```

对数组创建索引实际上就是对数组的每一个元素创建一个索引项，所以如果一篇文字有 20个评论，那么它就会有 20个索引项。这使得数组索引的代价比单值索引更高：对于单次的插入、更新或删除，每一个数组项可能都需要更新，非常庞大。

整个数组是无法作为一个实体创建索引的：对数组创建索引就是对数组中的每个元素创建索引。

数组元素上的索引并不包含任何位置信息：要查找特定位置的数组元素（如 "comments.4"），查询时无法使用索引的。

<br/>
<br/>

#### 多键索引的影响

如果一个索引有被索引的数组字段，则该索引会被立即标记为多键索引（explain 中的 isMultikey 为 true）。多键索引无法变成非多键索引，唯一方法是删除并重建这个索引。

多键索引可能会比非多键索引慢一些。可能会有许多索引项指向同一个文档，因此 MongoDB 在返回结果之前可能需要做一些删除重复数据的操作。

<br/>
<br/>

### 索引基数

**基数**（cardinality）是指集合中某个字段有多少个不同的值。有些字段，比如 "gender" 可能只有两个值，这类键的基数就非常低。其他一些字段，如 "username" 或 "email" 可能每个值都不相同，这类键的基数就非常高。还有一些字段介于两者之间，比如 "age" 或 "zip code"。

通常来说，一个字段的基数越高，这个字段上的索引就越有用。因为索引能够迅速将搜索范围缩小到一个比较小的结果集。对于基数比较低的字段，索引通常无法排查大量可能的匹配项。

根据经验来说，应该在基数比较高的键上创建索引，或者至少应该把基数比较高的键放在复合索引的前面（在低基数的键之前）。

<br/>
<br/>

## explain输出

对于慢查询来说，`explain` 是最重要的诊断工具，可以了解查询都使用了哪些索引以及是如何使用的。对于任何查询，都可以在默认添加一个 explain 调用，它必须是最后一个调用。

最常见的 explain 数据有两种类型：使用索引的查询和未使用索引的查询。特殊类型的索引可能会创建有不同的查询计划，但大多数字段应该是相似的。此外，分片返回的是多个 expalin 集合，因为查询会在多个服务器上执行。

如果一个查询不使用索引，则是因为它使用了 "COLLSCAN"。

expalin 输出的一些重要字段的详细介绍：

- **isMultiKey**：本次查询是否使用了多键索引。
- **nReturned**：本次查询返回的文档数量。
- **totalKeyExamined**：查找过的索引条目数量。。
- **totalDocsExamined**：按照指针索引在磁盘上查找实际文档的次数。
- **executionTimeMillis**：本次查询所花费的毫秒数。
- **stage**：是否可以使用索引完成本次查询。
- **needYield**：为了让写请求顺利进行，本次查询所做的让步（暂停）的次数。
- **indexBounds**：索引是如何被使用的，并给出了索引的遍历范围。
- **rejectedPlans**：拒绝的计划

<br/>
<br/>

## 何时不使用索引

索引在提取较小的子数据集时是最高效的，而有些查询在不适用索引时会更快。结果集在原集合中所占的百分比越大，索引就会越低效，因为使用索引需要进行两次查找：一次是查找索引项，一次是根据索引的指针去查找其指向的文档。而全表扫描只需查找文档。在最坏的情况下，使用索引进行查找的次数会是全表扫描的两边，通常会明显比全表扫描满。

| 索引通常适合的情况 | 全表扫描通常适合的情况 |
| - | - |
| 比较大的集合 | 比较小的集合 |
| 比较大的文档 | 比较小的文档 |
| 选择性查询 | 非选择性查询 |

<br/>
<br/>

## 索引类型

<br/>
<br/>

### 唯一索引

**唯一索引** 确保每个值最多只会在索引中出现一次（比如默认的 "_id"）。

<br/>
<br/>

### 部分索引

<br/>
<br/>

## 索引管理

每个集合只需要创建一次索引，如果再次尝试创建相同的索引，则不会执行任何操作。

关于数据库索引的所有信息都存储在 `system.indexes` 集合中。这是一个保留集合，因此不能修改或删除其中的文档。只能通过 `createIndex`, `createIndexes` 和 `dropIndexes` 命令来对它进行操作。

创建一个索引后，可以在 `system.indexes` 中看到它的云信息。也可以执行 `db.collectionName.getIndexes()` 来查看集合的所有索引的信息。

```js
// 查看集合的索引信息
db.users.getIndexes()
```

<br/>

创建新的索引既费时又耗费资源。在 MongoDB 4.2 之前，它会尽可能快地创建索引，阻塞数据库上的所有读写操作，直到索引创建完成。

如果希望数据库对读写保持一定的响应，那么可以在创建索引时使用 `background` 选项。这会迫使索引创建时不时让步于读写操作，但仍可能对应用程序的性能造成严重影响。后台创建索引也会比前台创建索引慢得多。

MongoDB 4.2 引入了一种新的方式，即混合索引创建，它只在索引创建的开始和结束时持有排他锁。创建过程的其余部分会交错地让步于读写操作。这种方式同时替换了前台和后台类型的索引创建。

如果可以选择，在现有文档中创建索引要比先创建索引然后插入所有文档中稍微快一些。

<br/>
<br/>

### 标识索引

集合中的每个索引都有一个可用于标识该索引的名称，默认形式是 `key1_dir1_key2_dir2_...keyN_dirN`（索引的键和方向）。可以自己指定索引名称，名称有字符数限制。

```js
db.users.createIndex({"username": 1, "age": 1}, {"background": true})

db.soup.createIndex({"a" : 1, "b" : 1, "c" : 1, ..., "z" : 1}, {"name" : "alphabet"})
```

<br/>
<br/>

### 修改索引

可以先创建新索引，然后删除旧索引。

```js
// 删除索引
db.people.dropIndex("x_1_y_1")
```

<br/>

---

<br/>

# 特殊类型的索引和集合类型

































<br/>

---

<br/>

# 副本集

本章内容如下：

- 什么是副本集
- 如何创建副本集
- 副本集成员有哪些可用的配置项


<br/>


## 复制简介

复制是将数据的相同副本保留在多台服务器上的一种方法，建议将其用于所有生产部署中。

在MongoDB中，创建**副本集(replica set)**后就可以使用复制功能。副本集是一组服务器，其中一个是用于处理写操作的**主节点(primary)**，还有多个用于保存主节点的数据副本的**从节点(secondary)**。如果主节点崩溃了，则从节点会从其中选取出一个新的主节点。


<br/>
<br/>


## 安全注意事项

配置副本集时，应该启用授权控制并指定身份认证。另外，最好对磁盘上的数据和副本集成员之间以及副本集与客户端之间的通信进行加密。


<br/>
<br/>


## 建立副本集

在生产环境中，应该始终使用副本集并为每个成员分配一个专用主机，以避免资源争用，并针对服务器故障提供隔离。为了提供更多的弹性，还应该使用DNS种子列表连接(seedlist connection)格式指定应用程序如何连接到副本集。

```bash
cd /test/mongodb/
mkdir ./rs{1,2,3}

# 启动副本
mongod --replSet mdbDefGuide --dbpath /test/mongodb/rs1 --port 17017 --smallfiles --oplogSize 200
mongod --replSet mdbDefGuide --dbpath /test/mongodb/rs2 --port 27017 --smallfiles --oplogSize 200
mongod --replSet mdbDefGuide --dbpath /test/mongodb/rs3 --port 37017 --smallfiles --oplogSize 200
```

<br/>

目前，每个mongod都不知道其他mongod的存在。为了能够彼此交互，需要创建一个包含每个成员的配置，并将此配置发送给其中一个mongod进程。它负责将此配置传播给其他成员。

副本集配置有几个重要组成部分。配置项`_id`是在命令行中传递的副本集名称，请确保此名称完全一致。副本集成员组成的数组，每个成员都需要`_id`和`host`这两个字段。

```js
rsconf = {
  _id: "mdbDefGuide",
  members: [
    {_id: 0, host: "localhost:27017"},
    {_id: 1, host: "localhost:27018"},
    {_id: 2, host: "localhost:27019"}
  ]
}

rs.initiate(rsconf)

rs.status()
```

这个配置文档就是副本集的配置。在某个mongod上运行的成员会解析配置并将消息发送给其他成员（不能在多个成员上使用数据初始化副本集），提醒它们存在新的配置。一旦所有成员都加载了配置，它们就会选择一个主节点并开始处理读写请求。

副本集会选举出一个主节点，可使用`rs.status()`或`db.isMaster()`命令来查看副本集的状态。

<br/>

> **注意**<br/>
> 不能在不停止运行的情况下将单机服务器转换为副本集，以重新启动并初始化该副本集。因此，即使一开始只有一台服务器，你也希望将其配置为一个单成员的副本集。这样，如果以后想添加更多成员，则可以在不停止运行的情况下进行添加。

<br/>

> **rs辅助函数**<br/>
> rs是一个含有复制辅助函数的全局变量。这些函数大部分是数据库命令的封装。<br>
> 如`rs.initiate(config)`命令等同于`db.adminCommand({"replSetInitiate": config})`。最好能同时熟悉辅助函数和底层命令，因为使用命令形式代替辅助函数可能会更简单。

<br/>
<br/>

## 观察副本集

连接到mongod，你应该会发现提示符发生变化: 

```mongo
mdbDefGuide:PRIMARY>
# 或
mdbDefGuide:SECONDARY>
```

<br/>

从节点可能会落后于主节点（延迟）而缺少最新的写入，所以默认情况下从节点会拒绝读请求，以防止应用程序意外读取过期数据。

**从节点不接受写操作**。从节点只能通过复制功能写入数据，不接受客户端的写请求。

因此，如果尝试在从节点上查询，则会弹出一条表明它不是主节点的错误消息。想要在从节点上进行查询操作，可以设置 **在从节点中读取是没有问题的** 标志。注意，`slaveOk` 是在 **连接(secondaryConn)** 上设置，而不是在数据库(secodaryDB)上。

```mongo
secondaryConn.setSlaveOk()

rs.slaveOk()

rs.secondaryOk()
```

<br/>

如果主节点停止运行，那么其中一个从节点将自动被选举为主节点。

需要注意的几个关键概念：

- 客户端在单台服务器上执行的请求都可以发送到主节点执行（读操作、写操作、执行命令、创建索引等）。
- 客户端不能在从节点上执行写操作。
- 默认情况下，客户端不能在从节点上读取数据，但可以启用从节点读取数据的功能。

<br/>
<br/>

## 更改副本集配置

可以随时更改副本集配置：添加、修改或删除成员。

```mongo
rs.add("localhost:27017")

rs.remove("localhost:27017")

rs.config()
var config = rs.config()
config.members[0].host = "localhost:27017"
# 配置文件修改正确，需要使用rs.reconfig()辅助函数将其发送到数据库
rs.reconfig(config)
# 每次更改副本集配置时，version字段都会自增。版本的初始值为1。
```

对于复杂的操作，比如更改成员配置或者一次性添加/删除多个成员，`rs.reconfig()`通常比`rs.add()`和`rs.remove()`更有用。可以使用这个命令来进行所需的任何合法的配置更改：只需要简单地创建代表所需配置的配置文档，然后将其传递给`rs.reconfig()`。

<br/>
<br/>

## 如何设计副本集

副本集中的重要概念——**大多数(majority)**。选举主节点时需要由大多数决定，这节点只有在得到大多数支持时才能继续作为主节点，写操作被复制到到多数成员时就是安全的写操作。这里的大多数定义为**副本集一半以上的成员**。

| 副本集成员总数 | 副本集大多数 |
| - | - |
| 1 | 1 |
| 2 | 2 |
| 3 | 2 |
| 4 | 3 |
| 5 | 3 |
| 6 | 4 |
| 7 | 4 |

也就是如下数学公式：$大多数 = n/2 + 1$

<br/>

如果副本集中只有少数节点可用，那么所有成员将变为从节点。

配置副本集时很重要的一点就是只能有一个主节点。MongoDB选择只支持单一主节点，这样可以使开发更容易，但是可能导致副本集变为只读状态。

<br/>

两种推荐的副本集配置方式：

- 将大多数成员放在一个数据中心
- 在两个数据中心各自放置相等的成员，在第三个地方放置一个用于打破僵局的副本集成员。


<br/>
<br/>


## 如何进行选举

> RAFT是一种共识算法，它被分解成了相对独立的子问题。共识是指对台服务器或进程在一些值上达成一致的过程。RAFT确保了一致性，使得同一序列的命令产生相同序列的结果，并在所部署的各个成员中达到相同序列的状态。

<br/>

当一个从节点无法与主节点连通时，它就会联系并请求其他的副本集成员将自己选举为主节点。其他成员会做几项健全性检查：它们能否连接到一个主节点，而这个主节点时发起选举的节点无法连接的？这个发起选举的从节点是否有最新数据？有没有其他更高优先级的成员可以被选举为主节点？

MongoDB在3.2版本中引入了第一版复制协议，基于斯坦福大学开发的**RAFT共识协议**。这是一个类RAFT的协议，并且包含了一些特定于MongoDB的副本集概念，比如仲裁节点、优先级、非选举成员、写入关注点(write concern)等。第一版协议提供了很多新特性的基础，比如更短的故障转移时间，以及大大减少了检测主节点失效的时间。它还通过使用term ID来防止重复投票。

<br/>

副本集成员相互间每隔两秒发送一次**心跳(heartbeat)**（相当于ping）。如果某个成员在10秒内没有反馈心跳，则其他成员会将该不良成员标记为无法访问。选举算法将尽**最大努力**尝试让具有最高优先级的从节点发起选举。成员优先级会影响选举的时机和结果。优先级高的从节点要比优先级低的从节点更快地发起选举，而且也更有可能成为主节点。然而，低优先级的从节点也可能短暂地被选为主节点，即使还存在一个可用的高优先级的从节点。副本集成员会继续发起选举知道可用的最高优先级成员被选为主节点。

就所有能连接到的成员，被选为主节点的成员必须拥有最新的复制数据。严格地说，所有的操作都必须比任何一个成员的操作都要高，因此所有的操作都必须比任何一个成员的操作都要晚。

<br/>
<br/>

## 成员配置选项

你可能希望让某个成员拥有优先选举成为主节点的权力，或者将某个成员设置为对客户端不可见以便阻止将请求发送给它。

<br/>

### 优先级

优先级用于表示一个成员渴望成为主节点的程度。它的取值范围是0到100，默认是1。将**priority**设置为0有特殊的含义：优先级为0的成员永远不可能成为主节点。这样的成员称为 **被动(passive)** 成员。

拥有最高优先级的成员总是会被选举为主节点（只要他能连接到副本集中的大多数成员，并且拥有最新的数据）。

```mongo
# 添加一个优先级为1.5的成员
rs.add({"host": "server-4:27017", "priority": 1.5})
```

设置优先级并不会导致副本集中无法选举出主节点，也不是使在数据同步中落后的成员称为主节点（一直到它的数据更新到最新）。

优先级(priority)的绝对值只与它是否大于或小于副本集中的其他优先级相关，优先级为100、1和1的一组成员，与优先级为2、1和1的另一组成员的行为方式相同。

<br/>
<br/>

### 隐藏成员

客户端不会向隐藏成员发送请求，隐藏成员也不会优先作为副本集的数据源（尽管当其他复制源不可用时隐藏成员也会被使用）。因此，很多人会将性能较弱的服务器或者备份服务器隐藏起来。

只有优先级为0的成员才能被隐藏，不能隐藏主节点。将hidden设置为true即可隐藏节点，要将隐藏成员设置非隐藏，只需将配置中的hidden设为false，或删除此选项。

```js
var config = rs.config()
config.members[3].priority = 0
config.members[3].hidden = true

rs.reconfig(config)
```

使用 `rs.status()` 和 `rs.config()` 能够看到隐藏成员，隐藏成员只对 `isMaster` 不可见。当客户端连接到副本集时，会调用 `isMaster` 来查看副本集中的成员。因此，隐藏成员永远不会收到客户端的读请求。

<br/>
<br/>

### 选举仲裁者

对于大多数需求来说，两节点的副本集具有明显的缺点。然而，许多小型部署不希望保存三份数据副本集，觉得两份副本集就足够了，而保存第三份副本集会付出不必要的管理、操作和财务成本。

MongoDB支持一种特殊类型的成员，称为 **仲裁者(arbiter)**，其唯一作用就是参与选举。仲裁者并不保存数据，也不会为客户端提供服务。它只是为了帮助具有两个成员的副本集满足 **大多数**这个条件。通常来说，最好使用没有仲裁者的部署。

由于仲裁者并不需要履行传统mongod服务器端的责任，因此可以将其作为轻量级进程运行在配置比较差的服务器上。如果可能，应将找那个踩着与其他成员分开，放在单独的 **故障域(failure domain)**，以便它以一个外部视角来看待副本集中的成员。

```mongo
// 添加仲裁者
// 有两种方式
rs.addArb("server-5:27017")
rs.add({"_id": 4, "host": "server-5:27017", "arbiterOnly": true})
```

成员一旦以仲裁者的身份被添加到副本集中，它就永远只能是仲裁者。无法将仲裁者重新配置为非仲裁者，反之亦然。

<br/>

使用仲裁者需要考虑以下几件事：

- **最多只能使用一个仲裁者**：添加额外的仲裁者并不能加快选举，也不能提供更好的数据安全性。
- **使用仲裁者的缺点**：在不知道将一个成员作为数据节点还是仲裁者时，应将其作为数据节点。如果可能，尽可能在副本集中使用奇数个数据成员，而不是使用仲裁者。

<br/>

> 在具有主-从-仲裁(PSA)架构的三成员副本集或具有PSA分片的分片集群中，如果两个数据节点的任何一个停止运行并启用了 `majority` 的 **读关注(read concern)**，则必然存在缓存压力增加的问题。理想情况下，应该将仲裁者替换为数据成员。或者为了防止存储缓存压力，可以在部署或分片中的每个mongod实例上禁用majority的读关注。

<br/>

注意两个关键词：

- 读关注(read concern)
- 写关注(write concern)

<br/>
<br/>

### 副本集创建索引

有时从节点不需要具有与主节点上相同的索引，甚至可以没有索引。如果仅使用从节点备份数据或脱机批量处理作业，则可以在成员配置中指定`"buildIndexed": false`，此选项可防止从节点创建任何索引。

与隐藏成员一样，此选项要求成员的优先级为0。

<br/>

---

<br/>

# 副本集的组成

本章介绍副本集的各个部分时如何组织在一起的，包括：

- 副本集成员如何复制新数据
- 如何让新成员开始工作
- 选举是如何进行的
- 可能出现的服务器端和网络故障场景

<br/>

## 同步

**复制** 是指在多台服务器上保持相同的数据副本。MongoDB实现此功能的方式是保存 **操作日志(oplog)**，其中包含了主节点执行的每一次写操作。oplog是存在于主节点local数据库中的一个固定集合。从节点通过查询此集合以获取需要复制的操作。

每个从节点都维护着自己的oplog，用来记录它从主节点复制的每个操作。这使得每个成员都可以被用作其他成员的**同步源**。从节点从同步源中获取操作，将其应用到自己的数据集上，然后再写入oplog中。如果应用某个操作失败（只有再基础数据已损坏或数据与主节点不一致时才会发生此情况），则从节点会停止从当前数据源复制数据。

如果一个从节点由于某种原因而停止运行，那么当它重新启动后，就会从oplog中的最后一个操作开始同步。由于这些操作是先应用到数据上然后再写入oplog，因此从节点可能会重复已经应用到其数据上的操作。MongoDB在设计时就考虑到了这种情况：将oplog中的同一个操作执行多次与执行一次的效果是一样的。oplog中的每个操作都是幂等的。也就是说，无论对目标数据集应用一次还是多次，oplog操作都会产生相同的结果。

由于oplog的大小是固定的，因此它只能容纳一定数量的操作。在大多数情况下，默认的oplog大小就足够了。但你也可以修改oplog的大小。

在mongod进程创建oplog之前，可以使用 `oplogSizeMB` 选项指定其大小。然而，在第一次启动副本集成员后，只能使用 `更改oplog大小` 这个流程来更改oplog。

<br/>

MongoDB中存在两种形式的数据同步：

- 初始化同步用于向新成员中添加完整的数据集
- 复制用于将正在发生的变更应用到整个数据集


<br/>
<br/>


### 初始化同步

MongoDB在执行初始化同步时，会将所有数据从副本集中的一个成员复制到另一个成员中。当一个副本集成员启动时，它会检查自身的有效状态，以确定是否可以开始从其他成员中同步数据。如果状态有效，它就会尝试从该副本集的另一个成员中复制数据的完整副本。

首先，MongoDB会克隆除local数据库之外的所有数据库。mongod会扫描源数据库中的每个集合，并将所有数据插入目标成员上这些集合的对应副本中。在开始克隆操作之前，目标成员上的任何现有数据都将被删除。

一旦所有的数据库都被克隆，mongod就会使用这些来自同步源的oplog记录来更新它的数据集以反映副本集的当前状态，并将复制过程中发生的所有变更应用到数据集上。成员在完成初始化同步后会过度到正常同步流程，这使其成为从节点。

从操作者的角度来看，进行初始化同步的过程非常容易，只需要一个干净的数据目录启动mongod。然而，更推荐从备份中进行恢复。从备份中恢复通常比通过mongod复制所有数据要快。

进行初始化同步时一个最常见的问题就是时间过长。在这种情况下，新成员可能会从同步源的oplog末尾脱离，由于同步源的oplog已经覆盖了成员计息复制所需的数据，因此新成员会远远落后于同步源并且无法再跟上。


<br/>
<br/>


### 复制

MongoDB执行的第二种同步是复制。从节点成员在初始化之后会持续复制数据。它们从同步源复制oplog，并在一个异步进程中应用这些操作。从节点可以根据需要自动更改同步源，以应对ping时间及其他成员复制状态的变化。

有一些规则可以控制给定节点从哪些成员进行同步。例如，拥有投票权的副本集成员不能从没有投票权的成员那里同步数据，从节点不能从延迟成员和隐藏成员那里同步数据。


<br/>
<br/>


### 处理过时数据

如果某个从节点远远落后于同步源当前的操作，那么这个从节点就是**过时**的。过时的从节点无法赶上同步源，因为同步源上的操作过于领先。如果继续同步，从节点就需要跳过一些操作。这种情况可能发生在以下场景中：

- 从节点服务器停止运行
- 写操作超过了自身处理能力
- 忙于处理过多的读请求

<br/>

当一个从节点过期时，它将依次尝试从副本集中的每个成员进行复制，看看是否有成员拥有更长的oplog以继续进行同步。如果没有一个成员拥有足够长的oplog，那么该成员上的复制将停止，并且需要重新进行完全同步或从最近的备份中恢复。

为了避免出现不同步的从节点，让主节点拥有一个比较大的oplog以保存足够多的操作日志很重要。根据经验，oplog应该可以覆盖两到三天的正常操作。



<br/>
<br/>



## 心跳

每个成员需要知道其他成员的状态：谁是主节点？谁可以作为同步源？谁停止运行了？

为了维护副本集的最新视图，所有成员每隔两秒会向副本集其他成员发送一个**心跳请求**。心跳请求用于检查每个成员的状态。

心跳的一个最重要的功能是让主节点知道自己是否满足副本集大多数的条件。如果主节点不再得到大多数节点的支持，它就会降级，成为一个从节点。


<br/>


### 成员状态

成员的一些状态：

- **STARTUP**：成员第一次启动时的状态，这时MongoDB正在尝试加载它副本集配置。一旦配置被加载，它就转换到STARTUP2状态。
- **STARTUP2**：初始同步过程会持续处于这个状态。然后转到RECOVERING。
- **RECOVERING**：成员运行正常，但不能处理请求。有以下原因：
  - 在启动时，成员必须做一些检查以确保自己处于有效的状态，之后才能接收读请求。因此，在启动过程中，所有成员在成为从节点之前都需要经历短暂的REVOVERING状态。在处理一些耗时操作时，成员也有可能进入此状态。
  - 如果一个成员远远落后于其他成员而无法赶上时，也会进入RECOVERING状态。通常来说，这是需要进行重新同步的无效状态。这时，该成员不会进入错误状态，因为它希望找到一个拥有足够长oplog的成员，从而引导自己回到非过时状态。
- **ARBITER**：仲裁节点独有的一种特殊状态，并在其正常运行期间应该始终处于此状态。
- **DOWN**：如果一个成员正常启动，但后来变为不可访问，那么就会进入这种状态。
- **UNKNOWN**：如果一个成员从未能访问到另一个成员，那么就不知道它处于什么状态。
- **REMOVED**：表示此成员以被从副本集中移除。如果被移除的成员被添加到副本集，它就会转换回正常的状态。
- **ROLLBACK**：当成员正在回滚数据时会处于此状态。在回滚结束时，服务器回转换回RECOVERING状态，然后成为从节点。


<br/>
<br/>



## 选举

当一个成员无法访问到主节点（而且本身有资格成为主节点）时，便会申请选举。申请选举的成员会向其所能访问的所有成员发出通知。如果这个成员不适合作为主节点那么其他成员会知道原因：如此成员的数据落后于副本集，或已有一个主节点在申请选举，而那个失败的成员无法访问到此节点。在这些情况下，其他成员将投票反对该成员的申请。

假如没有理由反对，其他成员就会为申请当选的成员投赞成票。如果申请选举的成员从副本集中获得了大多数选票，选举就成功了，该成员将过渡到PRIMARY状态。如果没有获得大多数选票，那么它会继续处于从节点状态，以后可能会试图再次成为主节点。主节点会一直处于主节点状态，知道不能满足大多数的要求、停止运行、降级，或者副本集被重新配置为止。



<br/>
<br/>



## 回滚

如果主节点执行一个写操作之后停止了运行，而从节点还没来得及复制此操作，那么新选举出的主节点可能会丢失这个写操作。

比如这个操作是126，而从节点上只有125操作。

当恢复后，服务器会寻找之前的126操作以开始与其他服务器的同步，但无法找到这个操作。当这种情况发生时，A和B将开始一个名为**回滚(rollback)**的过程。回滚用于撤销在故障转移前未复制的操作。具有126号操作的服务器会在另一个数据中心服务器的oplog中寻找公共的操作点。它们会发现125号操作是相互匹配的最后一个操作。

这时，服务器会遍历这些操作，并接受这些操作影响的每个文档写入一个bson文件，保存在数据目录下的rollback目录中。

一个经常被误用的成员配置选项是每个成员的投票数量设置。改变成员的投票数量通常不会得到想要的结果，并且会导致大量的回滚。除非做好了定期处理回滚的准备，否则不要更改成员的投票数量。


<br/>


### 当回滚失败时

在旧版本的MongoDB中，如果要回滚的内容太多，则可能导致回滚无法执行。从MongoDB 4.0开始，回滚的数据量就没有限制了。在4.0之前，如果要回滚的数据量超过300MB或者要回滚的操作超过30分钟，那么回滚可能会失败。在这些情况下，对于回滚失败的节点，必须重新进行同步。

这种情况最常见 的原因是从节点存在同步延迟，而主节点停止运行了。如果其中一个从节点变成了主节点，那么之前主节点中的许多操作会丢失。为了确保在回滚过程中不会失败，最好的方法是让从节点的数据尽可能保持最新。



<br/>

---

<br/>




# 从应用程序连接到副本集

本章介绍如何在应用程序中与副本集进行交互，包括：

- 如何连接到副本集以及故障转移的工作机制；
- 在进行写操作时等待复制；
- 将读请求路由到正确的成员。


<br/>


## 客户端到副本集的连接行为

MongoDB的客户端开发库（驱动程序）用于管理与MongoDB服务端的通信。对于副本集，默认情况下，驱动程序会连接到主节点，并将所有流量都路由到此节点。应用程序可以像与单机服务器通信一样执行读写操作，同时副本集会在后台悄悄地处理热备份。

种子列表就是服务器地址列表。种子时应用程序将读取和写入数据的副本集成员。你不需要列出种子列表中的所有成员（尽管也可以）。当驱动程序连接到种子服务器时，它可以从其中发现其他成员。

```
# 一个连接字符串类似于下
"mongodb://server-1:27017,server-2:27017,server-3:27017"
```

<br/>

所有MongoDB驱动程序都遵守服务器发现和监控(SDAM)规范。驱动程序会持续监视副本集的拓扑结构，以检测应用程序对集合中成员的访问能力是否有变化。此外，驱动程序会监视副本集，以维护关于哪个成员是主节点的信息。

如果一个主节点发生故障，则驱动程序会自动找到新的主节点，并将请求尽快路由到新的主节点。不过，当没有可达的主节点时，应用程序将无法执行写操作。

在选举过程中，主节点可能短暂地不可用。如果没有可达成员能够成为主节点，则主节点可能长时间不可用。默认情况下，驱动程序在此期间不会处理任何请求（无论读还是写）。如果应用需要，则可以配置驱动程序将读请求路由到从节点。

驱动程序处理MongoDB故障转移的策略：

- 不重试
- 在重试一定次数后放弃
- 最多只重试一次

MongoDB从3.6开始，服务端以及所有MongoDB驱动程序都支持可重试写选项。


<br/>
<br/>


## 在写入时等待复制

在某个时刻，一个从节点可能被选为主节点并开始接收新的写入。当之前的主节点恢复时，会发现它有一些不存在于新主节点上的写操作。为了纠正这一点，它会撤销与当前主节点的操作序列不匹配的任何写操作。这些操作不会丢失，而是会被写入特殊的回滚文件中，这些文件必须手动应用于当前的主节点。MongoDB不能自动应用这些写操作，因为它们可能与崩溃 后发生的其他写操作冲突。因此，这些写操作会消失，知道管理员有机会将回滚文件应用于当前的主节点。

如果对写入大多数成员有要求，则可以防止这种情况的出现。如果应用程序得到了写入成功的确认，那么新的主节点必须拥有该写入的副本。如果应用程序没有收到来自服务器端的确认消息或收到了错误消息，那么它会知道需要进行重试，因为在主节点崩溃之前，写操作没有传播到副本集的大多数成员。

因此，如果要保证无论副本集出现什么情况写操作都可以被持久化，那么必须确保每个写操作都传播到副本集的大多数成员。可以使用**写关注(writeConcern)**实现这一点。

```javascript
// javascript 写关注的示例
// 写关注指定为 majority(大多数)
try {
    db.products.insertOne(
        {"_id": 10, "item": "envelopes", "qty": 100, type: "Self-Sealing"},
        {"writeConcern": {"w": "majority", "wtimeout": 100}}
    );
} catche (e) {
    print (e);
}
```

但在这个写操作被复制到副本集的大多数成员之前，服务器端不会发出响应。只有这样，应用程序才会收到这个写操作成功的确认。如果在指定的超时时间之内没有写入成功，则服务器端会恢复一条错误消息。


<br/>
<br/>


## 自定义复制保证规则

副本集运行你创建可以传递给`getLastError`的自定义规则，以确保写操作被复制到所需的任何服务器组合上。

如：

- 保证复制到每个数据中心的一台服务器上
- 保证写操作被复制到大多数非隐藏节点
- 创建其他保证规则

尽管规则的理解和设置都比较复杂，但它是一种非常强大的副本集配置方式。除非有相当特殊的复制请求，否则使用`"w": "majority"`是非常安全的。


<br/>
<br/>



## 将读请求发送到从节点

默认情况下，驱动程序会将所有请求路由到主节点。但可以通过设置驱动程序的**读偏好(read preference)**来配置其他选项。

将读请求发送到从节点通常不是一个好主意。虽然这在某些特定的情况下是有意义的，但通常应该将所有请求发送到主节点。如果你正在考虑将读请求发动到从节点，那么请确保在此之前已非常仔细低权衡利弊。

读偏好的五种模式：

- primary(默认)
- primaryPreferred
- nearest
- secondary
- secondaryPreferred


<br/>


### 一致性考虑

对一致性要求非常高的应用程序不应该从从节点读取数据。

通常从节点会落后于主节点的时间在几毫秒之内。然而，这一点无法保证。驱动程序无法知道从节点的数据有多新，因此可能会将查询发送到一个远远落后的从节点上。因此，如果应用程序需要读取最新的数据，那么就不应该从从节点读取。


<br/>
<br/>


### 负载考虑

服务器过载通常会使它的执行速度变慢，进一步降低副本集的处理能力，迫使其他成员承担更多的负载，这样就会陷入恶性循环。

过载还会导致复制的速度变慢，使得剩余的从节点落后于主节点。突然间，你的副本集中有的成员崩溃了，有的成员发生了滞后，所有成员都过载了，没有任何回旋的余地。

一个更好的选择是使用分片来分配负载。


<br/>
<br/>


### 由从节点读取的场景

在某些情况下，将应用程序的读请求发送到从节点是合理的。

注意，如果应用程序需要低延迟读和低延迟写，则必须使用分片：副本集只允许在主节点上进行写操作（无论主节点在什么位置）。

如果从落后的从节点读取数据，则必须牺牲一致性。另外，如果希望等待写操作复制到所有成员，则需要牺牲写入速度。

如果应用程序确实能够接受陈旧的数据，那么可以使用`secondary`或`secondaryPreferred`作为读偏好。

- secondary：总是将读请求发送给从节点。如果没有可用的从节点，则会出现错误，而不是将请求发送给主节点。
- secondaryPreferred：如果从节点可用，则将读请求发送到从节点。否则，请求被发送到主节点。

<br/>

有时候，读负载与写负载有很大的不同，比如，正在读取的数据与正在写入的数据是完全不同的。为了进行离线处理，你可能需要很多索引，而又不希望将这些索引创建在主节点上。在这种情况下，可以设置一个具有与主节点不同索引的从节点。如果想以这种方式使用从节点，那么么就需要让驱动程序直接连接到从节点，而不是使用副本集连接。




<br/>

---

<br/>




# 副本集管理

本章介绍副本集管理的相关内容，包括：

- 对独立的成员进行维护；
- 对各种不同情况下配置副本集；
- 获取oplog相关信息以及调整oplog大小；
- 使用特殊的副本集配置；
- 将主从模式转换为副本集模式。


<br/>


## 以单机模式启动成员

许多维护任务不能在从节点上执行（因为涉及了写操作），也不应该在主节点上执行，因为这会对应用程序性能造成影响。因此，以下各节经常会提到以单机模式启动服务器。这意味着需要重新启动成员，使其成员单机运行的服务器，而不再是一个副本集的成员（只是临时的）。

保持dbpath不间，因为以这种方式重启是为了对这台服务器的数据进行一些操作。

```
# 查看用于启动的命令行选项
db.serverCmdLineOpts()

# 关闭服务器
db.shutdownServer()

# 以单机而不是副本启动
/path/bin/mongod --port 新端口 --dbpath /var/lib/db
# 可以需要添加一些额外的参数，才能正常启动，例如
# --bind_ip=127.0.0.1 --port=xxx --dbpath=xxx  --storageEngine=wiredTiger --directoryperdb --logpath=/var/log/mongod.log --fork
```

当完成了对服务器的维护后，可以使用原始的副本集选项重启启动它。重启之后，它会自动与副本集的其它成员进行同步。复制它在离开期间错过的所有操作。


<br/>
<br/>


## 副本集配置

副本集配置总是保存在`local.system.replset`集合的文档中。这个文档在副本集的所有成员上都是相同的。不要使用`update`更新这个文档，应该使用`rs`辅助函数或`replSetReconfig`命令。

<br/>

可以通过重新配置来修改成员的设置。修改成员设置时有一些限制：

- 不能更改成员的`_id`字段；
- 不能将接收重新配置命令的成员（通常是主节点）的优先级设置为0；
- 不能把仲裁者变为非仲裁者，反之亦然；
- 不能将成员的`buildIndexed`字段从false改为true。

<br/>

副本集成员最多只能有50个成员，其中只有7个成员拥有投票权。这是为了减少每个成员发送心跳所需的网络流量，并限制选举所需的时间。

如果要创建一个超过7个成员的副本集，那么额外的成员都必须被赋予0投票权(`"votes": 0`)，使这些成员无法在选举中投票。

```
# 创建副本集
var config = {
  "_id": 副本集名称,
  "members": [
    {xxxx},
    {xxx},
  ]
}
# 只需要对副本集中的一个成员调用rs.initiate()。接受配置的成员将把配置传递给其他成员。
rs.initiate(config)


# 更改副本集成员
rs.add()
# rs.remove()
var config = rs.confg()
config.members[2].host = "xxxx"
rs.reconfig(config)


# 无投票权的节点
rs.add({"_id": 7, "host": "server-7:27017", "votes": 0})
```



<br/>
<br/>



## 控制成员状态

有多种方式可以手动控制成员的状态，以进行维护或应对负载的变化。需要注意，无法强制一个成员成为主节点，只能对副本集进行适当的配置，即为副本集成员设置高于任何其他成员的优先级。

```
# 把主节点降级为从节点
# 使主节点降级为从并维持60s
rs.stepDown()
# 指定秒为单位的时间
rs.stepDown(300)


# 阻止选举
# 以秒为单位
# 强制让成员保持为从节点
rs.freeze(600)
# 释放，也可以使用此命令将已退位的主节点解冻
rs.freeze(0)
```



<br/>
<br/>



## 监控复制

能够监控副本集的状态是很重要的，不仅要监控所有成员是否启动，还要监控它们所处的状态以及数据的新旧程度。


<br/>


### 获取状态

`replSetGetStatus`或`rs.status()`命令可以获取副本集成员的当前信息。下面是一些有用的字段：

- `self`
- `stateStr`: 状态字符串
- `uptime`: 从成员可被访问一直到现在所经历的秒数
- `optimeDate`：每个成员的oplog中最后一个操作发生的时间
- `lastHeartbeat`：此服务器最后一次收到来自self这个成员心跳的时间
- `pingMs`：心跳达到此服务器的平均时间，这用于确定要从哪个成员进行同步
- `errmsg`：成员在心跳请求中选择返回的状态信息


<br/>
<br/>


### 可视化复制图谱

如果在从节点上运行`rs.status()`，则会有一个名为`syncingTo`的顶级字段，它表示成员正从哪个成员复制数据。MongoDB会根据ping的时间来决定同步源。

自动复制链(automatic replication chaining)有一个缺点：更多的复制链节点意味着将写操作复制到复制到所有服务器需要更长的时间。

MongoDB的复制路径可能会变成一条线（虽然可能性很低），复制链中的每个节点都比它前面的从节点落后一些。可以使用`replSetSyncFrom`命令(`rs.syncFrom()`)修改成员的复制源来解决这个问题。

```
# 从节点修改复制源
secondary.adminCommand({"replSetSyncFrom": "server-0:27017"})
```


<br/>
<br/>


### 复制循环

当几个成员彼此进行复制时，就发生了**复制循环**。

当成员自动选择同步源时，复制循环是不可能发生的。不过，使用`replSetSyncFrom`命令可能会强制复制循环发生。在手动更改同步目标之前，请仔细检查`rs.status()`的输出，注意不要造成循环。

当选择的同步成员并不比自身领先之时，`replSetSyncFrom`命令会给出警告，但仍然允许这样做。


<br/>
<br/>


### 禁用复制链

**链式复制**指一个从节点从另一个从节点（而不是主节点）进行同步。可以禁用复制链，通过将`chainingAllowed`设置为false（默认为true），强制每个成员从主节点进行同步。如果主节点不可用，那么它们就会从其它从节点同步数据。


<br/>
<br/>


### 计算延迟

对于复制来说，最重要的指标之一就是从节点和主节点之间的延迟情况。**延迟(lag)**是指从节点相对于主节点的落后程度，也就是主节点执行的最后一个操作的时间戳与从节点应用的最后一个操作的时间戳之间的差值。


```
# 可以使用下列命令来查看成员的复制状态
rs.status()

rs.printReplicationInfo()

rs.printSlaveReplicationInfo()
```


<br/>
<br/>


### 调整oplog大小

应该将主节点的oplog长度视为维护工作的时间窗口。如果主节点的oplog长度是一小时，那么就只有一小时的时间来修复所有的问题。因此，你通常会希望oplog可以保存几天到一周的数据据，以便在出现问题时给自己一些应对的空间。

要增加oplog的大小，请执行以下步骤。

- 连接到副本集
- 查看`local`库中oplog的当前大小
- 更改oplog的大小
- 如果减少了oplog的大小，可能需要运行`compact`命令来回收被分配出来的磁盘空间。不要对主节点运行此命令。

```
use local
# 以MB显示
db.oplog.rs.stats(1024*1024).maxSize

# 更改为16000M
db.adminCommand({replSetResizeOplog: 1, size: 16000})
```

一般情况下，不应该减少oplog的大小。oplog不会占用有价值的像RAM或CPU这样的资源，只要有足够的磁盘空间来容纳它即可。


<br/>
<br/>


### 创建索引

如果向主节点发送创建索引的命令，那么主节点会正常创建索引，然后从节点会在复制`创建索引`这条操作时进行索引的创建。

索引创建是资源密集型操作，可能会导致成员不可用。如果所有从节点同时创建索引，那么副本集中的大部分成员将处于离线状态，直到索引创建完成。这个过程只适用于副本集。

<br/>

> 注意:<br>
> 在创建`unique`索引时，必须停止对集合的所有写操作。如果没有停止写操作，那么整个副本集成员的数据可能会不一致。

<br/>

因此，你可能希望一次只在一个成员上创建索引，以最小化对应用程序的应用。步骤如下：

- 关闭一个从节点
- 将其以单机模式重新启动
- 在单机服务器上创建索引
- 当索引创建完成后，以副本集成员的身份重新启动服务器
- 对其他从节点执行上述步骤

副本集中除了主节点以外的每个成员都成功创建了索引。现在你有两个选择，应该根据实际情况选择对生产环境影响最小的那个：

- 在主节点上创建索引。如果系统有一段流量较少的空闲期，那么这可能是一个很好的创建索引的时机。你还可能修改读偏好，以便在创建过程中临时将更多负载分流到从节点。主节点仍然会把索引创建命令复制到从节点，但由于从节点已经有了这些索引，因此这不会产生任何操作。
- 将主节点退位为从节点，然后按照前面描述的步骤操作。

如果要创建唯一索引，请确保主节点中没有插入重复的数据，或者应该首先在主节点上创建索引。否则，主节点可能会插入重复数据，这将导致从节点上的复制错误。如果发生这种情况，那么从节点会自动关闭。你必须将其作为单机服务器重新启动，删除唯一索引，然后重新启动它。


<br/>
<br/>


### 在预算有限的情况下进行复制

如果预算有限，不能购买多台高性能服务器，则可以考虑将从节点服务器只用于灾难恢复，这样的服务器不需要太高的配置。始终将高性能服务器作为主节点，便宜的服务器不处理任何客户端流量（配置客户端所有请求发送到主节点）。

可以为这样的从节点配置以下选项：

```
# 节点永远不会成为主节点
"priority": 0

# 客户端不会向此从节点发送请求
"hidden": true

"buildIndexes": false

"votes": 0
```




<br/>

---

<br/>




# 分片

本章介绍如何扩展MongoDB，包括：

- 分片和集群组件
- 如何配置分片
- 分片与应用程序的交互

<br/>


## 什么是分片

**分片**是指扩机器拆分数据的过程，有时也用术语**分区(partitioning)**。

通过在每台机器上防止数据的子集，无须功能强大的机器，只使用大量功能稍弱的机器，就可以存储更多的数据并处理更多的负载。分片还可以用于其他目的，包括将经常访问的数据放置在更高性能的硬件上，或基于地理位置来拆分集合中的文档以使它们接近最常对其进行访问的应用服务器。

无论从开发还是运维的角度来看，分片都是最复杂的MongoDB配置。在使用分片集群之前，应该首先熟悉单机服务和副本集。

<br/>

可以在单台机器上快速启动一个分片集群，然后使用ShardingTest类创建集群。

```
mongo --nodb --norc

st = ShardingTest({
  name: "one-min-shards",
  chunkSize: 1,
  shards: 2,
  rs: {
    nodes: 3,
    oplogSize: 10
  },
  other: {
    enableBalancer: true
  }
});
```


<br/>
<br/>


## 理解集群组件

> **注意**<br>
> 许多人对复制和分片感到困惑。复制是在多台服务器上创建了数据的精确副本，因此每台服务器都是其他服务器的镜像。而每个分片包含了不同的数据子集。

MongoDB的分片机制允许你创建一个由许多机器（分片）组成的集群，并将集合中的数据分散在集群中，在每个分片上放置数据的一个子集。这允许应用程序超出超级服务器或副本集的资源限制。

记住，分片的主要使用场景是拆分数据集以解决硬件和成本的限制，或为应用程序提供更好的性能。

MongoDB分片集群的几个组件：

- mongos: 路由服务器。
- mongod config server：配置服务器
- mongod: 分片副本集

路由服务器知道哪些数据在哪个分片上，可以将请求转发到适当的分片。如果有对请求的响应，路由服务器会收集它们，并在必要时进行合并，然后在发送回应用程序。mongos读取的信息都位于config server中。

你不需要知道任何关于分片的信息，比如有多少个分片或它们的地址是什么。只要有分片存在，就可以将请求发送给mongos，并允许其转发到合适的分片上。

启用均衡器可以确保数据均匀分布在两个分片上。


<br/>
<br/>


## 进行分片

可以使用`sh.status()`来获得集群的总体视图。此命令会提供一个分片、数据库以及集合的摘要。

MongoDB现在还不能自动分发数据，因为它不知道你希望如何或者是否进行分发。你必须明确指出，在每个集合中应该如何分布数据。

<br>

要对一个特定的集合进行分片，首先需要在集合的数据库上启用分片。

在对集合进行分片时，需要选择一个**片键(shard key)**。片键是MongoDB用来拆分数据的一个或几个字段。

如果选择在`username`字段上分片，MongoDB就会根据用户名的范围对数据进行拆分。如`a1-xxx`到`defcon`，`defcon1`到`hohaha1998`，等等。可以将选择一个片键看作为集合中的数据选择一个排列顺序。这与索引的概念类似，也十分合理。

随着集合的增大，片键会成为集合中最重要的索引。只有创建了索引的字段才能够作为片键。选择片键需要仔细斟酌。

```
# 对accounts数据库启动分片
sh.enableSharding("accounts")

# 现在可以对accounts数据库中的集合进行分片了

# 在启用分片之前，必须在想要分片的键上创建一个索引
db.users.createIndex({"username": 1})

# 现在通过username来对集合进行分片
sh.shardCollection("accounts.users", {"username": 1})
```

可以看到集合被分成了多少块，每个块是数据的一个子集。这些是按照片键范围排列的。

```
# 数据库范围
# "on": shard部分 表示分布在那个分片上
{"username": minValue} -->> {"username": maxValue}
```

在分片之前，集合实际上是一个单独的块。分片根据片键将其拆分成更小的块(chunk)。

注意块列表开始和结束的键，即`$minKey`和`$maxKey`。minKey可被认为是负无穷，这个值比MongoDB中的其他值都要小。类似地，maxKey相当于正无穷，它比任何其它值都要打。因此，总会在块范围中看到这两个极值。片键的值始终位于这两者之间。

这两个值实际上是BSON类型，不应该用在应用程序中，它们主要是供内部使用。如果希望在shell中引用它们，可以使用`MinKey`和`MaxKey`常量。

<br/>

现在可以使用explain来看看MongoDB在幕后是如何处理的。通常来说，如果在查询中没有使用片键，mongos就不得不将查询发送到所有分片上。

```
db.usrs.find({username: "user12345"}).explain()
```

包含片键并可以发送到单个分片或分片子集的查询称为**定向查询(targeted query)**。必须发送到所有分片的查询称为**分散-收集查询(scatter-gather query)**，也称为广播查询：mongos会将查询分散到所有分片，然后再从各个分片收集结果。




<br/>

---

<br/>




# 配置分片

主要介绍：

- 如何创建配置服务器、分片以及mongos进程
- 如何增加集群的容量
- 数据是如何存储和分布的


<br/>
<br/>


## 何时分片

通常情况下，分片用于：

- 增加可用RAM
- 增加可用磁盘空间
- 减少服务器的负载
- 处理单个mongod无法承受的吞吐量


<br/>
<br/>


## 启动服务器


<br/>


### 配置服务器

配置服务器(configsvr)是集群的大脑，保存在关于每个服务器包含哪些数据的所有元数据。因此，必须首先创建配置服务器。configsvr选项向Mongod表明将其用作配置服务器。在运行此选项的服务器上，除了`config`和`admin`库之外，客户端不能对任何其他数据库写入数据。

由于配置服务器所保存的数据非常重要，因此必须确保它在运行时启用了日志功能，并确保它的数据存储在非临时性的驱动器上。在生产环境中，配置服务器副本集至少应该包含3个成员（一主两从），每个配置服务器应该位于单独的物理机器上。

admin数据库包含了与身份认证和授权相关的集合，以及其他以`system.*`开头的集合以供内部使用。

config数据库包含了保存分片集群元数据的集合。在元数据发生变化（比如数据块迁移、数据块拆分等）时，MongoDB会将数据写入此库。

当对配置服务器进行写入时，MongoDB会使用`majority`的`writeConcern`级别。类似地，当从配置服务器读取数据时，MongoDB会使用`majority`的`readConcern`级别。这确保了分片集群元数据在不发生回滚的情况下才会被提交到配置服务器副本集。它还确保了只有那些不受配置服务器故障影响的元数据才能被读取。这可以确保所有mongos路由节点对分片集群中的数据组织方式具有一致的看法。

<br/>

> 如果所有配置服务器都丢失了，那么你必须对分片上的数据进行分析，以确定数据的位置。这是可以做到的，但过程缓慢且令人厌烦。应该经常备份配置服务器的数据。在执行任何集群维护之前，应该总是对配置服务器进行备份。


<br/>
<br/>


### mongos进程

在三个配置服务器都运行后，启动一个或多个mongos进程以供应用程序进行连接。mongos进程需要知道配置服务器的地址，因此需要使用`configdb`配置项。

默认情况下，mongos运行在27017端口上。注意，不需要指定数据目录（mongos本身没有数据，它在启动时从配置服务器加载集群配置）

应该启动一定数量的mongos进程，确保高可用。并尽可能将其放在靠近所有分片的位置。这样可以提高需要访问多个分片或执行分散-收集操作时的查询性能。


<br/>
<br/>


### 将副本集转换为分片

分片集群的分片mongod实例必须指定`shardsvr`配置项。创建一个分片实例的副本集（如rs0）。

然后连接到mongos添加分片:

```
sh.addShard("rs0/mongo1:port,mongo2:port,mongo3:port")
```

集合名rs0被用作 这个分片的标识符。如果想删除这个分片或将数据迁移到其中，可以使用rs0来对其进行标识。

将副本集作为分片添加到集群中后，就可以将应用程序从连接到副本集修改为连接到mongos了。在添加这个分片时，mongos会将副本集中的所有数据库注册为分片所拥有的数据库，因此它会将所有的查询发送到新分片上。mongos还会像客户端一样自动处理应用程序的故障转移，也同样会将错误返回。

<br/>

> 在添加分片之后，必须设置所有客户端将请求发送到mongos而不是副本集。


<br/>
<br/>


### 增加集群容量

如果需要更多的容量，则可以添加更多的分片。要添加新的分片，可以先创建一个副本集。确保副本集与任何其他分片具有不同的名称。然后通过mongos运行`addShard`命令将其加入到集群中。

如果有几个现有的副本集不是分片，那么只要没有任何同名的数据库，就可以将它们全部作为新分片添加到集群中。如果有一个副本集，库名与分片中的库名冲突，那么mongos将拒绝将其添加到集群中。


<br/>
<br/>


### 数据分片

只有在明确制定了规则之后，MongoDB才会对数据进行拆分。在希望对数据进行拆分时，必须明确地告知数据库和集合。

```
// 为数据库启用分片
sh.enableSharding("testdb")

// 对集合进行分片
sh.shardCollection("testdb.users", {"name": 1})
```

现在users集合会按照name键分片，如果是对一个已经存在的集合进行分片，则必须在name字段上有索引，否则，`shardCollection`调用将返回错误。如果出现了错误，则需要先创建索引，并重新运行`shardCollection`命令。

如果要分片的集合还不存在，则mongos会自动在片键上创建索引。

`shardCollection`命令会将集合拆分成多个数据块，这些块是MongoDB用来移动数据的单元。一旦命令成功返回，MongoDB就会开始在集群中的分片间均匀地分散聚合中的数据。这个过程不是瞬间完成的。对于大型集合来说，完成这一初始平衡可能需要数小时。这段时间可以通过预拆分来缩短。即在加载数据之前，预先在分片上创建数据块。之后加载的数据就会直接插入当前分片，而不再需要额外的平衡。


<br/>
<br/>


## MongoDB如何追踪集群数据

每个mongos都必须能够根据给定的片键来找到一个文档。理论上，MongoDB可以跟踪每个文档的位置，但对于包含数百万或数十亿的集合来说，这种方式会变得难以处理。因此，MongoDB会将文档以**数据块**形式进行分组，这些数据块是片键指定范围内的文档。块总是存在于分片上，因此MongoDB可以用一个较小的表来维护数据块和分片的映射。

如果一个用户集合的片键是`{"age": 1}`，那么某个块可能是有所有`age`字段在3和17之间的文档组成。如果mongos收到一个`{"age": 5}`的查询，那么它就可以将该查询路由到该块所在的分片上。

<br/>

> 块与块之间的范围不能重叠。<br>
> 一个文档总是术语且仅术语一个块。这条规则意味着，不能使用数组字段作为片键，因为MongoDB会为数组创建多个索引项。<br/>
> 一旦一个块增长到一定的大小，MongoDB就会自动将它分成两个更小的块。<br/>
> 一个常见的误解是，同一个块的数据应保存在磁盘的同一片区域中。这是不正确的。块对mongod如何存储集合中的数据没有影响。


<br/>
<br/>


### 块范围

每个块都是由它所包含的文档范围来描述的。新分片的集合起初只有一个块，所有文档都位于这个块中。该块的边界从负无穷(`$minKey`)到正无穷(`$maxKey`)。

随着块的增长，MongoDB会自动将其拆分为两个块，范围分别是从负无穷到某个值，某个值到无穷大。这里的某个值被称为**拆分点(split point)**。

块信息存储在`config.chunks`集合中。


<br/>
<br/>


### 拆分块

各个分片的主节点mongod进程会跟踪它们当前的块，一旦达到某个阈值，就会检查该块是否需要拆分。如果该块确实需要拆分，那么mongod会从配置服务器请求全局块大小配置值，然后执行块拆分并更新配置服务器上的元数据。配置服务器会创建新的块文档，并修改旧块的范围。如果该块位于分片顶部，则mongod会请求均衡器将其移动到其他分片上。这种方式 是为了防止在片键单调递增的情况下，某个分片成为热点。

因此，拥有不同的片键值很重要。

如果mongod试图进行拆分时其中一个配置服务器停止运行，那么mongod将无法更新元数据。在进行拆分时，所有的配置服务器都必须启动并可以识别。如果mongod不断收到对一个块的写请求，则它会持续尝试拆分该块失败。只要配置服务器没有处于健康状态，拆分就无法继续，而所有这些拆分尝试都会拖慢mongod和涉及的分片。

mongod反复尝试分裂某个块却无法成功的过程成为**拆分风暴(split storm)**。防止拆分风暴的唯一方法是确保配置服务器尽可能正常运行。


<br/>
<br/>


### 均衡器

**均衡器(balancer)**负责数据的迁移。它会定期检查分片之间是否存在不均衡，如果存在，就会对块进行迁移。

在MongoDB v3.4之后的版本中，均衡器位于配置服务器副本集的主节点成员上。在MongoDB v3.4及之前的版本中，每个mongos会偶尔扮演均衡器的角色。

均衡器是配置服务器副本集主节点上的后台进程，它会监视每个分片上的块数量。只有当一个分片的块数量达到特定迁移阈值时，均衡器才会被激活。

假设一些集合已经达到了阈值，则均衡器会开始对块进行迁移。它会从负载较大的分片中选择一个块，并询问该分片是否应该在迁移之前对块进行拆分。在完成必要的拆分后，就会将块迁移到具有较少块的机器上。

使用集群的应用程序不需要感知数据的迁移：所有读写请求都会路由到旧的块上，知道迁移完成。一旦元数据被更新，任何试图访问旧位置数据的mongos进程都会收到一个错误。这个错误对客户端是不可见的，mongos会默默地处理这个错误并在新的分片上重试此操作。

有时可能会在mongos日志中看到"unable to setShardVersion"的信息，这是一个常见的错误。当mongos收到这种类型的错误时，它会从配置服务器查找数据的新位置，并更新块分布表，然后重新执行之前的请求。如果成功从新位置检索到数据，则会将数据返回给客户端，就像没有发生过任何错误一样。

如果mongos因配置服务器不可用而无法检索到新块的位置，则它会向客户端返回一个错误。这也是让配置服务器始终处于正常运行状态非常重要的另一个原因。


<br/>
<br/>


## 排序规则

MongoDB中的排序规则允许指定特定于语言的字符串比较规则。


<br/>
<br/>


## 变更流

**变更流(change stream)**允许应用程序跟踪数据块中数据的实时变更。




<br/>

---

<br/>




# 选择片键

使用分片时最重要的任务是选择数据的分发方式。必须了解MongoDB是如何分发数据的。包括：

- 如何在多个可用的片键中做出选择
- 不同使用场景中的片键选择
- 哪些键不能作为片键
- 自定义数据分发方式的可选策略
- 如何手动对数据分片


<br/>


## 评估使用情况

> 注意:<br>
> 一旦对一个集合进行了分片，就不能更改片键了。

在对集合进行分片时，需要选择一两个字段来对数据进行拆分。这个键（这些键）称为**片键**。一旦对一个集合进行了分片，就不能更改片键了。因此正确选择片键是十分重要的。

对于计划分片的每个集合，首先回答以下问题：

- 计划进行多少个分片? 3个分片的集群比100个分片的集群具有更大的灵活性。随着集群的增长，不应该使用会触发所有分片的查询，因此大部分查询应该包含片键。
- 分片是为了减少读写延迟吗？降低写延迟通常包括将请求发送到地理位置更近或功能更强大的机器上。
- 分片是为了提高读写的吞吐量吗？（吞吐量指集群在同一时间可以处理的请求数量。）提高吞吐量通常需要增加更多的并行化，并确保请求在集群中均匀分发。
- 分片是为了增加系统资源吗？如果这样，你可能希望保持工作集尽可能小。


<br/>
<br/>


## 描绘分发情况

最常见的数据拆分方式是**升序片键**、**随机分发的片键**和**基于位置的片键**。


<br/>


### 升序片键

升序片键通常类似与`date`字段或`ObjectId`，随着时间稳步增长的字段。自增主键是升序字段的另一个例子。

**最大块**(max chunk)，持续增长，并被拆分成多个块。

这种模式通常会使MongoDB更难保持块的均衡，因为所有的块都是由一个分片创建的。因此，MongoDB必须不断地将数据块移动到其它分片上，而不能像在一个更均匀分发的系统中那样，只纠正一些可能出现的比较小的不均衡。

> 在MongoDB 4.2中，自动拆分的功能被移动到了分片主节点的mongod中，这增加了对顶部数据块的优化，从而得以解决升序片键模式的问题。均衡器会决定在哪个分片中放置顶部块。这有助于避免在一个分片上创建所有新块的情况。


<br/>
<br/>


### 随机分发的片键

随机分发的片键可以是用户名、电子邮件、UUID、MD5哈希值或数据集中没有可识别模式的任何其它键。

随着更多的数据被插入，数据的随机性意味着新插入的数据应该相当均匀地名中每个块。

由于写操作是随机分发的，因此分片应该以大致相同的速度增长，从而减少需要进行的迁移操作数量。

随机分发片键的唯一缺点是MongoDB在随机访问超出RAM大小的数据时效率不高。但是，如果有足够的资源或者不介意性能影响，那么随机片键可以很好地在集群中分配负载。


<br/>
<br/>


### 基于位置的片键

基于位置的片键可以是用户的IP、经纬度或地址。无论如何，基于位置的键就是将具有某些相似性的文档根据这个字段划分进同一个范围。这对于将数据放在离用户很近的地方以及将相关数据保存在磁盘的同一块区域中都很方便。MongoDB使用区域分片(zoned sharding)对其进行管理。

> 在MongoDB 4.0.3以上版本中，可以在对集合进行分片之前定义区域以及区域的范围，这回针对区域范围和片键的值填充数据块，并执行他们的初始块分配。这大大降低了分片区域设置的复杂性。


<br/>
<br/>


## 片键策略

为各种类型的应用程序提供一些片键选择。


<br/>


### 哈希片键

为了尽可能快地加载数据，哈希片键是最好的选择。哈希片键可以使任何字段随机分发。因此，如果打算在大量查询中使用升序键，但又希望写操作随机分发，那么哈希片键是不错的选择。

不过，我们永远都无法使用哈希片键执行指定目标的范围查询。如果不打算执行范围查询，那么哈希片键是一个很好的选择。

```
# 要创建哈希片键，首先需要创建一个哈希索引
db.users.createIndex({"username": "hashed"})

# 接下来对集合进行分片
sh.shardCollection("app.users", {"username": "hashed"})
```

> 使用哈希片键有一些限制。首先，不能使用unique选项。其次，与其他片键一样，不能使用数组字段。最后，浮点型的值在哈希之前会被取整，因此1和1.9999会被哈希定义为相同的值。


<br/>
<br/>


### GridFS的哈希片键


<br/>
<br/>


### 消防水管策略

如果有一些服务器比其他服务器更强大，那么你可能希望让它们处理更多的负载。可以强制将所有新数据插入功能更强大的分片中，然后让均衡器将旧的块移动到其他分片上。这样可以提供较低的写入延迟。

这种策略的另一个缺点是需要一些变更来进行扩展。如果最强大的服务器无法再写入的数量，则没有简单的方法可以再这台服务器和另一台服务器之间分配负载。

如果没有高性能的服务器，或者没有使用区域分片，就不要使用升序键作为片键。这样做会将所有写操作都路由到同一个分片上。


<br/>
<br/>


### 多热点

创建多个热点，以便写操作在集群中均匀分发，但在同一个分片中的写操作是递增的。



<br/>
<br/>



## 片键规则和指导方针

确定要分片的键并创建片键会让人想起索引，因为这两个概念是相似的。事实上，通常你的片键可能就是你最常使用的索引（或索引的一些变体）。


<br/>


### 片键的限制

片键不能是数组。如果任何键有数组值，那么`sh.shardCollection()`就会失败，并且将数组插入该字段是不允许的。

文档在插入之后，起片键值可能会被修改，除非片键字段是不可变的`_id`字段。在MongoDB4.2之前的旧版本中，是不可以修改文档的片键值的。

大多数特殊类型的索引不能用作片键。特别是，不能在地理空间索引上进行分片。如前所述，允许使用哈希索引作为片键。


<br/>
<br/>


### 片键的基数

无论片键是跳跃的还是稳定增长的，选择值会发生变化的键很重要。

与索引一样，在高基数字段上进行分片的性能会更好。如果一个`logLevel`键只有`DEBUG`, `WARN`和`ERROR`这几个值，则MongoDB无法将数据拆分成3个以上的块（因为片键只有3个不同的值）。如果想把一个变化不大的值用作片键，那么可以使用改键和另一个拥有多样值的键组成一个复合键。比如`logLevel`和`timestamp`。

重要的是，键的组合要具有很高的基数。


<br/>
<br/>


## 控制数据分发

也就是手动控制分发。


<br/>


### 对多个数据库和集合使用一个集群

向MongoDB明确指定你希望数据被保存的位置。

使用`sh.addShardToZone()`辅助函数。如将不同的集合分配给不同的分片（重要的数据集合分配到高性能的服务器，低价值的数据集合分配到低性能的服务器）。

为集合指定区域键范围不会立即生效。它只是给均衡器一条指令，说明当它运行时，可以将集合移动到这些目标分片上。

如果出现失误或改变了注意，可以使用`sh.removeShardFromZone()`从区域中删除分片。

如果从某个区域键范围内的区域中移除了所有分片，则均衡器不会再将数据分发到任何地方，因为没有任何位置是有效的。所有数据仍然是可读且可写的，除非修改标签或标签范围，否则它无法呗迁移到其他位置。


<br/>
<br/>


### 手动分片

如果不希望对数据进行自动分发，可以关闭均衡器，并使用`moveChunk`命令手动分发数据。

```
# 禁用均衡器
# 连接到mongos
sh.stopBalancer()
```

如果当前正在进行迁移，则此设置在迁移完成之前不会生效。然而，一旦正在进行的迁移完成，均衡器就会停止移动数据。

然而，除非遇到特殊情况，否则应该使用MongoDB的自动分片而不是手动分片。如果某个分片上出现了一个预料之外的热点，那么大部分数据可能会出现在这个分片上。

尤其不要再均衡器开启时手动进行一些不寻常的分发。如果均衡器检测到块的数量不均匀，那么它回对数据进行调整和重新分发，使集合再次均衡。




<br/>

---

<br/>










# 分片管理

手动管理分片集群，包括一下几项内容：

- 检查集群状态：集群有哪些成员，数据保存在哪里？哪些连接是打开的？
- 添加、删除以及修改集群的成员
- 管理数据移动和手动移动数据


<br/>


## 查看当前状态


<br/>


### 查看摘要信息

```
# 提供了分片、数据库以及分片集合的概要信息
# 它显示的所有信息都是从config数据库中收集的
sh.status()

```


<br/>


### 查看配置信息

分片集群的所有配置信息都保存在配置服务器(configsvr)的config数据库中。

通常来说，不应该直接更改config数据库中的任何数据。如果确实修改了，也需要重启所有的mongos进程才能看到效果。

config数据库中的一些集合:

- `config.shards`: shards集合会跟踪集群中的所有分片
- `config.databases`: databases集合会跟踪集群所知道的所有数据库，既包括分片数据库，也包括非分片数据库
- `config.collections`: collections集合会跟踪所有分片集合的信息，不显示非分片集合
- `config.chunks`: chunks集合会保存集合中每个块的记录
- `config.changelog`: changelog集合对于跟踪集群的当前操作非常有用，它记录了所有已经发生的拆分和迁移

<br/>

```
db.shards.find()
{ "_id": "shard01",
  "host": "shard01/h1:p1,h2:p2,h3:p3",
  "state": 1
}
{ "_id": "shard02",
  "host": "shard02/h1:p1,h2:p2,h3:p3",
  "state": 1
}
...

_id是从副本集名称中获取的，因此集群中的每个副本集必须有一个唯一的名称。
```

<br/>

```
db.databases.find()
{ "_id": "video", "primary": "shard02", "partitioned": true,
  "version": { "uuid": UUID("xxx-xx-xxx"),
  "lastMod: 1}
}
```

如果已经在数据库上运行过`enablesharding`，则`partitioned`字段将为`true`。`primary`是数据库的主基地。默认情况下，数据库中的所有新集合都会在数据库的主分片上创建。

<br/>

```
db.collections.find().pretty()
{
  "_id": "config.system.session",
  "lastmodEpoch": ObjectId("xxxx"),
  "lastmod": ISODate("1970-02-xxxx"),
  "dropped": false,
  "key": {
    "_id": 1
  },
  "unique": false,
  "uuid": UUID("xxxxx")
}
...

_id 集合的命名空间
key 片键
unique 表明片键是否是唯一索引
```

<br/>

```
db.chunks.find().skip(1).limit(1).pretty()
{
  "_id": "video.movies-imdbId_MinKey",
  "lastmod": Timestamp(2, 0),
  "lastmodEpoch": ObjectId("xxxxx"),
  "ns": "video.movies",
  "min": {
    "imdbId": { "$minKey": 1}
  },
  "max": {
    "imdbId": NumberLong("-xxxxxx")
  },
  "shard": "shard01",
  "history": [
    {
      "validAfter": Timestamp(xxxx, xxx),
      "shard": "shard01"
    }
  ]
}

_id 块的唯一标识符。通常包括命名空间、片键和块的下边界值
ns 块所属集合名称
min 块范围的最小值
max 块中的所有值都小于这个值
shard 块所属的分片
```


<br/>
<br/>


## 跟踪网络连接

集群组件之间有大量连接。


<br/>


### 获取连接统计

connPoolStats命令返回从当前数据实例到分片集群或副本集其他成员的连接信息。

```
db.adminCommand({"connPollStats": 1})
```

- `totalAvailable`显示了从当前mongod或mongos实例向分片集群或副本集其他成员的可用传出连接总数
- `totalCreated`报告了当前mongod或mongos实例向分片集群或副本集其他成员创建的传出连接总数
- `totalInUse`提供了从当前mongod或mongos实例向当前正在使用的分片集群或副本集其他成员的传出连接总数
- `totalRefreshing`显示了从当前mongod或mongos实例向当前正在刷新的分片集群或副本集其他成员的传出连接总数
- `numClientConnections`标识了了从当前mongod或mongos实例向分片集群或副本集其他成员的活动并被保存的传出同步连接数量
- `numAScopedConnection`报告了从当前mongod或mongos实例向分片集群或副本其他成员的活动并被保存的传出作用域同步连接数量
- `pools`显示了按连接池分组的连接统计信息（正在使用/可用/已创建/刷新）。mongod或mongos有两个不同的外传连接池
  - 基于DBClient的连接池
  - 基于NetworkInterfaceTL的连接池
- `hosts`显示了按主机分组的连接统计信息。它报告了当前mongod或mongos实例与分片集群或副本集每个成员之间的连接。


<br/>
<br/>


### 限制连接数量

当客户端连接到mongos时，mongos会创建一个连接，此连接至少会连接到一个分片以传递客户端的请求。因此，每个连接到mongos的客户端至少会产生一个从mongos到分片的传出连接。

如果有多个mongos进程，则可能会创建超过分片处理能力的连接。默认情况下，一个mongos(mongod)可以接受65535个连接。可以在mongos配置中使用`maxConns`选项来限制其可以创建的连接数。

一个分片可以处理的单个mongos的最大连接数公式: 

$$maxConns = maxConnsPrimary - (numMembersPerReplicaSet * 3) - \frac{(other * 3)}{numMongosProcesses}$$

```
# 主节点上的最大连接数，通常为20000，避免mongos的连接冲垮分片
maxConnsPrimary

# 主节点会与每个从节点创建一个连接，而每个从节点会与主节点创建两个连接，所以总共有3个连接
numMembersPerReplicaSet*3

# 指可能连接到mongos的各种进程的数量
other*3

# 分片集群中mongos的总数
numMongosProcesses
```

<br/>

注意，maxConns只会阻止mongos创建超过这个数量的连接。当达到这个限制时，它不会做任何有帮助的事情，它只会阻塞请求，并等待连接被释放。因此，必须防止应用程序使用这么多的连接，特别是mongos进程的数量不断增长时。


<br/>
<br/>


## 服务器管理

如何添加和删除服务器。


<br/>


### 添加服务器

可以在任何时候添加mongos进程，确保配置正确的配置服务器地址就行。

可以使用`addShard`命令添加新的分片。


<br/>
<br/>


### 修改分片中的服务器

要更改一个分片的成员，需要直接连接到该分片的主节点（不是通过mongos，而是连接到副本的主节点），重新配置副本集。集群配置会检测到变更并自动更新`config.shards`，不要手动修改`config.shards`。


<br/>
<br/>


### 删除分片

通常来说，不应该从集群中删除分片。如果经常添加和删除分片，则会给系统带来不必要的压力。如果添加了过多的分片，那么最好让系统增长到这些分片的体量，而不是先删除分片然后等需要时再添加。

首先确保均衡器时打开的。均衡器的任务是把要删除分片上的所有数据移动到其他分片上，这个过程称为**排空(draining)**。要排空数据，可以运行`removeShard`命令，将该分片上的所有块移动到其他分片上。

```
db.adminCommand({"removeShard": "shard03"})
# 再次运行此命令来获得当前的状态
```


<br/>
<br/>


## 数据均衡

通常来说，MongoDB会自动处理数据均衡。本节介绍如何启用和禁用自动均衡，以及如何干预均衡的过程。


<br/>


### 均衡器

关闭均衡器时大部分管理操作的先决条件。

```
sh.setBalancerState(false)
```

随着均衡器被关闭，新的均衡器流程将不会再开始，但是关闭均衡器不会迫使正在进行的均衡过程立即停止，也就是说，迁移过程通常不能立即停止。因此，应该检查`config.locks`集合以查看是否仍有均衡过程正在进行。

```
db.locks.find({"_id": "balancer})["state"]
# 0 表示均衡器已关闭
```

均衡过程会增加系统的负载，目标分片必须查询源分片块中的所有文档，将文档插入目标分片的块中，然后源分片必须删除这些文档。


<br/>
<br/>


### 修改块的大小

默认情况下，块的大小为64MB。块大小的取值范围在1MB到1024MB。

这是一个集群范围的设置，它会影响所有的集合和数据库。如果MongoDB的迁移过于频繁或所使用的文档太大，则可能需要增加块的大小。


<br/>
<br/>


### 移动块

可以使用`moveChunk`辅助函数对块进行手动移动。必须使用片键来查找要移动的块。


<br/>
<br/>


### 超大块

当一个块大于`config.settings`中设置的最大块大小时，均衡器就不允许移动这个块了。这些不可拆分、不可移动的块被称为**超大块(jumbo chunk)**，这种块非常难以处理。

在使用`sh.status()`查看时，超大块会被标记具有jumbo属性。

```
sh.status()

...
{ "x": -7 }  -->> { "x": 5 } on : shard001
{ "x": 5 }  -->> { "x": 6} on : shard0001 jumbo
...
```

要修复因超大块而引起的集群不均衡，就必须将超大块均匀地分配到各个分片中。

对于超大块的问题，应该优先避免这种情况的出现。


<br/>
<br/>


### 刷新配置

有时候mongos不能从配置服务器正确更新配置，则可以使用`flushRouterConfig`命令手动清除所有缓存。

```
db.adminCommand({"flushRouterConfig": 1})
```

如果执行了上述命令仍没有解决问题，则需要重启所有的mongos或mongod进程以清除所有缓存数据。




<br/>

---

<br/>




# 了解程序的动态

了解MongoDB正在作什么，以及细节情况。将学到：

- 找出并终止慢操作
- 获取并解析有关集合和数据库的统计数据
- 使用命令行工具来获得MongoDB正在作什么的信息


<br/>


## 查看当前操作

使用`db.currentOp()`函数查看正在运行的操作。

```
db.currentOp()
...
```

- `opid`: 操作的唯一标识符
- `active`: 操作是否正在运行
- `secs_running`: 操作的持续时间
- `op`: 操作类型
  - `query`
  - `insert`
  - `update`
  - `remove`
- `desc`: 客户端的标识符。这个字段可与日志中的消息相关联。
- `locks`: 描述操作所获取的锁的类型
- `waitingForLock`: 操作当前是否处于阻塞并等待获取锁
- `numYields`: 操作释放锁以允许其他操作进行的次数
- `lockstats.timeAcquiringMicros`: 操作为了获取锁所花费的时间

可以通过过滤currentOp来查找满足特定条件的操作:

```
db.currentOp(
{
  "active": true,
  "sec_running": { "$gt": 3 },
  "ns": /^db1\./
})
```


<br/>


### 寻找有问题的操作

`db.currentOp()`最常见的用途是查找慢操作。可以带条件来查找超过一定时间的查询，这些查询可能是缺少索引或对不适当的字段进行了过滤。


<br/>


### 终止操作

如果找到了想要停止的操作，那么可以将`opid`作为参数传递给`db.killOp()`来终止它。

```
db.killOp(12345)
```

并不是所有的操作都能被终止，持有或等待锁的操作不能被终止。

在MongoDB 4.0中，killOp可以在mongos上运行。在以前的版本中，需要在每个分片的主节点实例上手动发出终止命令。


<br/>


### 假象

在查找耗时过长的操作时，可能会看到结果中列出了一些长时间运行的内部操作。最常见的是复制线程和用于分片的回写监听器。任何在`local.oplog.rs`上长时间运行的请求以及任何回写监听命令都可以被忽略。


<br/>


### 防止幻象操作

如果在加载数据时使用了未确认写入的机制，那么应用程序触发写操作的速度可能比MongoDB处理它们的速度更快。如果MongoDB中的请求发生了堆积，那么这些写操作将堆积在操作系统的套接字缓冲区中。当终止MongoDB正在进行的写操作时，就会让MongoDB开始处理缓冲区中的写操作。即使客户端停止发送写操作，MongoDB也会处理那些写入缓冲区的操作，因为它们已经被接收了（只是没有被处理）。

防止这些幻象写入的最好方法是执行写入确认机制，让每次写操作都等待，知道前一个写操作完成，而不是仅仅等到前一个写操作处于数据库服务器的缓冲区中就开始下一次写入。


<br/>
<br/>


## 使用系统分析器

要查找速度较慢的操作，可以使用系统分析器，它会在一个特殊的`system.profile`集合中对操作进行记录。分析器可以提供大量关于耗时过长操作的信息，但这是有代价的。它会降低mongod的整体性能。

默认情况下，分析器是关闭的。分析级别不是持久的，重启数据库会清除级别的设定值。

```
db.getProfilingLevel()

# 记录超过10s
db.setProfilingLevel(1, 10000)
{"was": 0, "slowms": 100, "ok": 1}


db.system.profile.find().pretty()


db.setProfilingLevel(0)
```

将slowns设置较低通常不是一个好主意。即使分析器关闭，slowms也会对mongod产生影响，因为它决定了在日志中打印慢速操作的阈值。如果将slowms设置为100，那么每个耗时超过100ms的操作都将显示在日志中，即使分析器是关闭的。因此，如果调低了slowns来分析某些东西，那么可能需要在关闭分析器之前将它重新调高。


<br/>
<br/>


## 计算大小

为了配置正确的磁盘和RAM，了解文档、索引、集合和数据库占用了多少空间是很有用的。


<br/>


### 文档大小

使用`Object.bsonsize()`函数获取文档大小。

```
Object.bsonsize(db.user.findOne())
```

此方法显示出文档在磁盘上占用了多少字节。不过，这并不包括填充或索引，而二者是影响集合大小的重要因素。


<br/>


### 集合大小

`stats()`函数可以查看整个集合的信息:

```
db.movies.stats()
{
  "ns": "test.movies",
  "size": "1234567",
  "count": 12345,
  "avgObjSize": 123,
  "storageSize": 1122334,
  ...
}


db.big.stats(1024*1024*1024)
```

随着集合不断增长，阅读数十亿字节或更大字节的stats输出可能会变得很困难。因此，可以传入一个缩放因子作为参数：1024表示KB, `1024*1024`表示MB，以此类推。


<br/>
<br/>


### 数据库大小

数据库的`stats`函数与集合类似:

```
db.stats()
{
  "db": "sample_mflix",
  "collections": 5,
  "views": 0,
  "objects": 98308,
  "avgObjSize": 819.868,
  "dataSize": 80599593,
  "storageSize": 53620736,
  "numExtents": 0,
  "indexes": 12,
  "indexSize": 47001600,
  "scaleFactor": 1,
  "fsUsedSize": 355637043200,
  "fsTotalSize": 499963174912,
  "ok": 1
}
```


<br/>
<br/>


## 使用mongotop和mongostat

```
mongotop

# 获取每个数据库的锁统计信息
mongotop --locks


# 提供整个服务器范围的信息
# 快速了解数据库正在做什么
mongostat
```

mongostat的输出内容:

- insert/query/update/delete/getmore/command: 每种操作发生次数的简单计数
- flushes: 将数据刷新到磁盘的次数
- mapped: 映射的内存数量，这大约等于数据目录的大小
- vsize: 使用的虚拟内存数量，这通常是数据目录大小的两倍（一倍用于映射文件，一倍用于记录日志）
- res: 正在使用的内存数量
- locked db: 在上一个时间片中锁定时间最长的数据库。这个百分比是根据数据库被锁定的时间结合全局锁被持有的时间来计算的，这意味着值可能超过100%
- idx miss: 导致缺页错误的索引访问百分比（由于要查找的索引条目或索引内容不在内存中，因此mongod必须到磁盘中去读取）
- qr/qw: 读操作和写操作的队列大小（比如有多少读操作和写操作正处于阻塞中，等待被处理）
- ar/aw: 有多少活跃的客户端（比如当前执行读操作或写操作的客户端）
- netIn: 网络传入字节数
- netOut: 网络传出字节数
- conn: 打开的连接数，包括传入和传出
- time: 进行这些统计所花费的时间


<br/>

---

<br/>



# 安全介绍

为了保护MongoDB集群及其中的数据，可以采用一下安全措施：

- 启用授权并执行身份验证
- 对通信进行加密
- 对数据进行加密

通过使用MongoDB对x.509的支持来配置身份验证和传输层加密，以确保MongoDB副本集中客户端和服务段之间的安全通信。


<br/>


## 身份认证和授权


<br/>


### 身份验证机制

在MongoDB集群上启用授权会强制进行验证，并确保用户只能执行搜全的操作，这是由用户的角色决定的。MongoDB社区版提供了对SCRAM和x.509证书验证的支持。

x.509数字证书使用了被广泛接受的x.509公钥基础设施(PKI)标准来验证公钥的所有人。


<br/>
<br/>


### 授权

MongoDB在添加用户时，必须在指定的数据库中创建此用户。该数据库时针对此用户的身份验证数据库。对于身份验证，可以使用任何数据库（一般使用admin库）。

用户名和身份验证数据库一起作为用户的唯一标识符。然而，用户的权限并不局限在身份验证数据库中。在创建用户时，可以为其指定任何资源上（集群、库、集合）的操作权限。

MongoDB默认不启用身份验证和授权，需要显示启用它们。

配置副本集，首先在不启用认证和授权的情况下启用，然后创建用户，启用认证，重启进程。

<br/>

MongoDB提供的内置角色：

- read: 读取所有非系统集合中的的数据
- readWrite: 读取和修改非系统集合的数据
- dbAdmin: 执行管理任务
- userAdmin: 创建和修改角色及用户
- dbOwner: 结合了readWrite, dbAdmin, userAdmin这三个权限
- clusterManger: 对集群进行管理和监控
- clusterMonitor: 为监控工具提供只读的访问权限
- hostManager: 监控和管理服务器
- clusterAdmin: 结合了clusterManager, clusterMonitor, hostManager, dropDatabase的权限
- backup: 提供了足够的权限来备份整个实例
- restore: 提供了除去`system.profile`集合的备份中恢复数据的权限
- readAnyDatabase: 除去local和config，提供读取的权限，以及在集群上执行listDatabase的权限
- readWriteAnyDatabase: 除去local和config，提供读写的权限，以及在集群上执行listDatabase的权限
- userAdminAnyDatabase: 除去local和config，提供userAdmin的权限（实际上就是超级用户角色）
- dbAdminAnyDatabase: 除去local和config，提供adAdmin的权限，以及在集群上执行listDatabase的权限
- root: 所有权限
- 用户自定义角色


<br/>
<br/>


### 使用x.509证书对成员和客户端进行身份验证


<br/>
<br/>



## 认证和传输层加密教程




<br/>

---

<br/>




# 持久性

持久性是数据库系统的一种属性，它保证了提交给数据库的写操作将永久保存在数据库中。对于MongoDB来说，需要考虑的是集群（更具体地说是副本集）级别的持久性。

本章内容：

- MongoDB如果通过日志(journal)机制保证副本集成员级别的持久性
- MongoDB如何使用写关注(writeConcern)来保证集群级别的持久性
- 如何配置应用程序和MongoDB集群，以提供所需的持久性级别
- MongoDB如何使用读关注保证集群级别的持久性
- 如何在副本集中设置事务的持久性级别

<br/>

## 使用日志机制的成员级别持久性

为了在服务器发生故障时提供持久性，MongoDB使用了一种称为 **日志(journal)** 的 **预写式(WAL)机制**。WAL是数据库系统中一种常用的持久性技术，其基本原理是，在将对数据库所作的更改应用到数据库本身之前，将对这些更改的一种表示写道持久介质（如磁盘）上。

从MongoDB4.0开始，当应用程序对副本集执行写操作时，MongoDB会使用与oplog相同的格式创建日志条目。oplog中的语句是对写操作影响的每个文档所做的实际更改的表示。因此，oplog语句很容易应用于副本集的其他成员，而无须考虑版本、硬件或副本集成员之间的其他差异。此外，每个oplog语句都是幂等的，这意味着它可以被应用任意次数，而对数据库的更改结果总是相同的。

像大多数数据库一样，MongoDB同时维护了日志和数据库文件的内存试图。默认情况下，它每50毫秒会将日志条目刷新到磁盘上，每60秒会将数据库文件刷新到磁盘上。刷新数据文件的60秒间隔称为 **检查点(checkpoint)**。日志用于为上一个检查点以来写入的数据提供持久性。关于持久性的问题，如果服务器突然停了了，那么在其重新启动时，可以使用日志重放在关闭前没有刷新到磁盘的所有写操作。

<br/>
<br/>

## 使用写关注的集群级别持久性

通过写关注(writeConcern)，可以指定应用程序在响应写请求时需要何种级别的确认。

<br/>

mongodb查询语言支持为所有插入和更新方法指定写关注。假设有一个电子商务应用程序，希望确保所有的订单都是持久的。

```mongo
try {
  db.test.insertOne(
    {sku: "t111", item: "test ha", quantity: 3},
    {writeConcern: {w: "majority", wtimeout: 100}}
  );
} catche (e) {
    print (e);
}
```

这个示例写关注表示，希望得到服务器的确认。只有当写入成功被成功复制到副本集的大多数成员时才能算成功完成。此外，如果没有在100ms内复制到大多数副本集成员，则应该返回错误。这种情况下，MongoDB不会撤销写关注超过时间限制之前成功执行的数据修改，而应该由应用程序决定如何处理这种情况下的超时。

<br/>

还可以在写关注中使用j选项(journal)来要求对写操作的日志写入情况进行确认。

```mongo
try {
  db.test.insertOne(
    {sku: "t111", item: "test ha", quantity: 3},
    {writeConcern: {w: "majority", wtimeout: 100, j: true}}
  );
} catche (e) {
    print (e);
}
```

<br/>

在解决持久性问题时，必须仔细评估应用程序的需求，并权衡设置对性能的影响。



<br/>
<br/>


## 使用读关注的集群级别持久性

在MongoDB中，读关注(readConcern)允许对合适读取结果进行配置。这可以让客户端在写操作被持久化之前就看到写入的结果。读关注可以和写关注一起使用，以控制对应用程序的一致性和可用性的保证级别。

不要将读关注和读偏好(read preference)相混淆，读偏好处理从何处读取数据（默认是从主节点读取）。

读关注决定了正在读取的数据的一致性和隔离性。默认的readConcern是local，它所返回的数据不保证已经被写入了大多数承载数据的副本集成员。这可能导致数据在将来的某个时刻被回滚。majority读关注只返回被大多数副本集成员确认的持久数据。

<br/>
<br/>

## 使用写关注的事务持久性

<br/>
<br/>

## 检查数据损坏

validate命令可用于检查集合是否损坏。查看数据结果中的valid字段是否为true，否则它会给出所发现数据损坏细节。

```mongo
db.test.validate({full: true})
```

<br/>

---

<br/>

# 生产环境的配置

包括:

- 常用选项
- 启动和停止
- 安全相关的选项
- 日志相关的注意事项


<br/>


## 启动

启动方式:

- 命令行参数启动
- 配置文件启动

MongoDB的配置文件使用YAML格式。


<br/>
<br/>


## 停止

安全停止正在运行的MongoDB服务器和能够启动服务器一样重要。

有几种方式:

- 使用`shutdown`命令
- 使用`kill`发送信号

当在主节点运行时，shutdown命令在关闭服务器之前会将主节点退位，并等待从节点追赶上同步进度。这可以将回滚的可能性降到最低，但无法保证关闭的成功（也就是可能不会被关闭）。

```
# 温柔的关闭
use admin
db.shutdownServer()

# 强制关闭
db.adminCommand({"shutdown": 1, "force": true})


# 发送关闭信号
kill PID
```


<br/>
<br/>


## 安全性

应该尽可能严格地限制外部对MongoDB的访问。最好的方法是设置防火墙，只允许内部网络地址对MongoDB的访问。

```
bind_ip: 指定监听端口，内部地址最好
nounixsocket: 禁用Unix套接字的监听。如果不打算通过文件系统套接字进行连接，那么同样可以禁用此选项。
noscripting: 禁用服务器的JavaScript的执行
```

<br/>

MongoDB企业版提供了数据加密的功能，但MongoDB的社区版不支持这些选项。


<br/>
<br/>


## SSL连接

MongoDB支持使用TLS/SSL对传输进行加密。默认情况下，连接到MongoDB的数据传输是不加密的。


<br/>
<br/>


## 日志

默认情况下，mongod会将日志发送到标准输出。

日志级别的更改。

MongoDB默认会记录运行超过100ms的查询信息。可以通过setProfilingLevel更改此值。

```
logpath: 将日志发送到文件
logappend: 在使用日志文件的前提下，日志追加
```



<br/>

---

<br/>



# 监控

包括:

- 内存使用情况
- 应用程序的性能指标
- 诊断复制中的问题


<br/>


## 内存使用情况

访问内存中的数据速度很快，而访问磁盘中的数据速度会比较慢。计算机中一般会有容量小、昂贵但访问速度快的内存，以及容器大、便宜但访问速度慢的磁盘。

当请求存储在磁盘上（还不在内存中）的数据页时，系统回发生缺页错误(page fault)，并将该页从磁盘复制到内存中。然后就可以快速地访问内存中的数据页了。如果程序没有定期使用该页的内容，并且内存又被其他页占满，那么旧页就会从内存中呗清除，只存在于磁盘上。

将一个页面从磁盘复制到内存，比从内存中读取这个页面花费的时间要长的多。因此，MongoDB从磁盘复制数据的次数越少越好。


<br/>
<br/>


### 跟踪内存使用情况

MongoDB会报告三种类型的内存：常驻内存、虚拟内存和映射内存。

常驻内存(resident)是MongoDB在RAM中显式拥有的内存。如果查询一个文档，并将其加载进内存中，那么这个页面就会被添加到MongoDB的常驻内存中。

MongoDB会获得该页的地址。这个地址不是RAM中页面的真实地址，而是一个虚拟地址。MongoDB可以将它传递给内核，内核会查找出页面的真正位置。这样，如果内核需要从内核中清除该页面，那么MongoDB仍可以使用该地址对其进行访问。它会向内核请求内存，然后内核会查看页面缓存，如果发现页面不存在，就产生缺页错误并将页面复制到内存中，最后再返回给MongoDB。

MongoDB的映射内存包括MongoDB曾经访问过的所有数据。它的大小通常和整个数据集的大小差不多。

虚拟内存是操作系统提供的一种抽象，它对软件进程隐藏了物理存储的细节。每个进程都可以看到一个连续的内存地址空间。

MongoDB内存指标网往相当稳定，但随着数据集的增长，虚拟内存也会随之增长。常驻内存会增长到可用RAM的大小。


<br/>
<br/>


### 跟踪缺页错误

缺页错误数量表示MongoDB所查找的数据不在RAM中的频率。

无论应用程序能否处理这些延迟，当磁盘超载时，缺页错误都会成为一个问题。磁盘能够处理的负载量不是线性的：一旦磁盘超载，每个操作都必须等待越来越长的时间，从而引发连锁反应。通常存在一个临界点，超出临界点后磁盘性能会迅速下降。因此，应该尽量避免磁盘在其最大负载下运转。

<br/>

> 应该不断跟踪缺页错误的数量。如果在缺页错误达到某一数量时应用程序运行良好，那么就有了一个系统可以处理多少缺页错误的基线。如果随着缺页错误的上升性能开始下降，那么就有了一个系统可以处理多少缺页错误的继线。如果随着缺页错误的上升性能开始下降，那么就有了一个应该发出告警的阈值。


<br/>
<br/>


### IO等待

缺页错误通常与CPU空闲等待磁盘响应(IO等待)的时间密切相关。一些IO等待是正常的，MongoDB有时不得不去磁盘读取数据，并且无法完全避免对其他操作的妨碍。重要的是，要确保IO等待不会持续等在或接近100%。

IO等待处于100%，表明磁盘正在超载。


<br/>
<br/>


## 计算工作集的大小

通常来说，内存中的数据越多，MongoDB的运行速度就越快。因此，按照从最快到最慢的顺序，应用程序可能有以下几种情况：

1. 整个数据集都在内存中。这非常好，但通常代价很大或不可行。对于某些依赖于快速响应时间的应用程序来说，这可能是必要的。
2. 工作集在内存中。这是最常见的选择。工作集是应用程序使用的数据和索引。这可能是其所有内容，但通常会有一个涵盖90%请求的核心数据集（比如users集合和最近一个月的活动）。如果这个工作集能够放入RAM中，那么MongoDB的运行速度通常会很快。只有在遇到一些不寻常的请求时才需要访问磁盘。
3. 索引在内存中。
4. 索引的工作集在内存中。
5. 内存中没有可用的数据子集。如果可能的话，应该避免这种情况。这回非常慢。

只有知道工作集的内容和大小，才能知道是否可以将其保存在内存中。计算工作集大小的最佳方法时跟踪分析常用的操作，以确定应用程序的读写量。假设应用程序每周会创建2GB的新数据，其中800MB的数据时经常被访问的。用户倾向于访问最近一个月的数据，超过一个月的数据通常不会被用到。这样工作集的大小大约是3.2GB(800x4)，加上预估的索引大小，总共为5GB。


<br/>
<br/>


## 跟踪性能情况

跟踪查询的性能并使其保持稳定通常很重要。

对于MongoDB来说，CPU的大部分占用时间与IO相关。WiredTiger存储引擎是多线程的，可以利用额外的CPU核。然而，如果用户或系统时间接近100%（或100%乘以CPU数量），那么最常见的原因是缺少了某个常用查询的索引。跟踪CPU使用情况是一个很好的方法，这样可以确保所有查询都按照其期望的方式运行。

另一个类似的指标是队列长度，即有多少请求在等待MongoDB的处理。当一个请求在等待读操作或写操作的锁时，即被认为是在队列中。

可以查看进入队列的请求数量以判断是否发生了请求堆积。通常，队列大小的数值应该较低。一个很长且始终存在的队列表示mongod无法处理这个负载。这时应该尽快降低该服务器上的负载。


<br/>
<br/>


## 跟踪剩余空间

磁盘使用情况也是一个重要的监控指标。

当磁盘空间不足时，有以下几种选择：

- 如果正在使用分片，那么可以再添加一个分片。
- 删除未使用的索引。可以对特定集合使用`$indexStats`聚合来识别它们。
- 如果还没有进行过要锁操作，那么可以再一个从节点上执行压缩来看看是否有帮助。这通常只有在从集合中删除了大量数据或索引且没有新数据替换的情况下才有用。
- 关闭副本集的成员（一次一个），将其数据复制到更大的磁盘中并进行挂载。重新启动该成员，然后继续对下一个成员重复此操作。
- 用较大驱动器的成员替换副本集中的成员，添加新成员，让新成员追上旧成员，然后删除旧成员。
- 如果使用了directoryperdb选项，并且数据库增长速度非常快，则可以将数据库移动到其自身的驱动器中。然后将磁盘卷作为一个数据目录进行挂载。这样其他数据就不需要移动。


<br/>
<br/>


## 监控复制情况

复制延迟和oplog长度是需要跟踪的重要指标。延迟是指从节点无法跟上主节点的速度。延迟应该尽可能接近于0，并且通常是毫秒级别的。

如果从节点复制写操作的速度比主节点的写入速度慢，就会出现非零的延迟。这可能是由于网络问题或缺少`_id`索引造成的。要使复制正常工作，每个集合都需要这个索引。

如果一个集合缺少`_id`索引，则将服务器从副本集中脱离并作为单机服务启动，然后创建`_id`索引。确保将`_id`索引创建为唯一索引。一旦创建，`_id`索引就不能被删除或更改（除非删除整个集合）。

这节点不会为了帮助从节点赶上来而限制写操作，因此在一个超载的系统中，从节点落后很常见。可以在写关注中使用`w`，在一定程度上强制对主节点进行限制。

写操作比较少的系统会产生延迟的假象。在一个负载非常低的系统中，可能会看到另一个有趣的现象。复制延迟突然出现峰值。这其实并不是延迟，而是由抽样变化引起的。

另一个需要跟踪的重要的复制指标是每个成员的oplog长度。通常来说，只要有足够的磁盘空间，oplog就应该就可能长。oplog的使用方式基本上不占用任何内存，而一个长的oplog可能意味着运维体验上的天壤之别。



<br/>

---

<br/>



# 备份和恢复

定期对系统进行备份很重要。本章涵盖了几种常用的备份选项：

- 对单一服务器进行备份，包括快照的备份和恢复；
- 对副本集进行备份时的特别考虑；
- 对分片集群进行备份。

只有在紧急情况下有信心迅速完成对备份的部署时，备份才是有用的。因此，对于选择任何备份技术，都要确保同时对备份和恢复的操作进行练习，知道恢复过程为止。

<br/>
<br/>

## 对服务器进行备份

有多种方法可以创建备份。但无论那种方法，备份操作都会对系统造成压力，因此，备份应该在从节点上空闲时进行。

<br/>
<br/>

### 文件系统快照

文件系统快照使用系统级别的工具创建MongoDB数据文件设备的副本。此方式耗时很短，并且很可靠。

<br/>
<br/>

### 磁盘快照

直接给整个磁盘定期打快照。

<br/>
<br/>

### 复制数据文件

单机服务器，复制数据目录中的所有内容。因为没有文件系统支持的情况下无法同时复制所有文件，所以在进行复制时必须防止数据文件发生变化。此方法很慢。

```js
// 将写入刷新到磁盘并锁定数据库，从而防止后续写入。
db.fsyncLock();

// 执行 创建快照 中描述的备份操作

// 创建快照后，解锁数据库。
db.fsyncUnlock();
```

<br/>
<br/>

## MongoDB工具进行备份

使用 `mongodump` 和 `mongorestore` 备份和恢复数据。

此工具与 BSON 数据转储配合，可用于创建小型部署的备份。要实现弹性、无中断的备份，请使用文件系统快照或磁盘快照。

此工具会与 mongod 实例交互来进行操作，会影响当前数据库的性能，会很慢（无论是创建还是恢复），并且在处理副本集时也存在一些问题。

<br/>

实践：

- 为文件添加标签，以便你能够识别备份的内容以及备份对应的时间点。
- 有性能影响，请注意。
- 为确保能对副本集进行一致备份，必须使用 `--oplog` 选项捕获备份操作过程中接收到的写入。
- 试着将备份恢复到一个测试实例，来确保备份数据的可用性。
- 为了帮助减少分片集群备份中出现的不一致的可能性，你必须停止均衡器，停止所有写入操作，并在备份期间停止任何模式转换。

<br/>

使用实例：

```sh
# 备份 test 库
mongodump -h="host:port" -u="xx" -p='xx' --authenticationDatabase="admin" --db="test" --out test-bak

# 一些有用参数:
# --query: 指定查询条件
# --oplog: 拷贝源数据库中的所有数据以及从备份过程开始到结束的所有 oplog 条目

# 恢复 test 库
mongorestore -h="host:port" -u="xx" -p='xx' --authenticationDatabase="admin" test-bak/
```

<br/>
<br/>

## 备份恢复副本集

> 你无法将单个数据集恢复为三个新的 mongod 实例，然后创建副本集。如果你将数据集复制到每个 mongod 实例，然后创建副本集，MongoDB 将强制从节点执行初始同步。

<br/>

在备份副本集时，除了所需数据之外，还需要获取副本集的状态，以确保生成整个部署集群的准确时间点快照。

通常，应该在从节点上进行备份。建议使用快照的方式。

当启用复制时，`mongodump` 的使用就不那么简单了。必须使用 `--oplog` 选项，以获得某个时间点的快照。否则备份的状态会与集群中任何其他成员的状态都不匹配。恢复时，还必须创建一份 oplog ，否则被恢复的成员就不知道它被同步到哪里了。

要从 `mongodump` 备份恢复副本集成员，需要将目标副本集成员作为单机服务器启动，并使用 `--oplogReplay`选 项在其上运行 `mongorestore`。

<br/>
<br/>

### 使用快照和oplog恢复数据

比如，需要通过全量的快照加上增量的 oplog 恢复部分被删除的数据。

步骤：

- 导出需要的 oplog。
- 使用最近的快照在测试机器上恢复数据。
- 使用 oplog 恢复特定时间的增量数据。
- 找到缺少的数据，然后恢复到需要的环境中。

<br/>

```js
// 找到 test 库的特定时间 oplog，注意将时间戳转换为 unix 原子时间，注意时区
use local;
db.oplog.rs.findOne({
  "ns": /^test\\..+/,
  "ts": {
    "$gte": Timestamp(1688166000, 0), // 2023-07-01 00:00:00
    "$lte": Timestamp(1690758000, 0)  // 2023-07-31 23:59:59
  }
})
```

```sh
# 通过 mongodump 导出 oplog
mongodump -h="host:port" -u="xx" -p='xx' --authenticationDatabase="admin" --query='{"ns": /^test\\..+/, "ts": {"$gte": Timestamp(1688166000, 0), "$lte": Timestamp(1690758000, 0)}}' --out test-oplog

# 重放 oplog，找到删除之前的时间点
# 需要一个空目录
mkdir empty
mongorestore -h="host:port" -u="xx" -p='xx' --authenticationDatabase="admin" --oplogReplay --oplogFile="test-oplog/oplog.rs.bson" --oplogLimit "具体时间戳:1" empty/
```

<br/>
<br/>

## 备份恢复分片集群

在处理分片集群时，我们会将重点放在对部分组件的备份上：单独备份配置服务器和副本集。

在分片集群上执行任何备份或恢复操作之前都需要先关闭均衡器。

方法包括：

- 使用快照
- 使用数据库转储
- 注意配置集和副本集

<br/>

---

<br/>

# 部署

生产环境部署的相关建议：

- 建议使用 SSD
- 磁盘阵列建议 RAID-10
- 不要使用网络磁盘
- MongoDB 对 CPU 的负载不高。如果在速度和核数间选择，应该选择速度。
- 64 位 Linux 操作系统的稳定版本
- 内存根据实际情况配置
- 时钟同步
- 建议关闭 SWAP 交换空间
- 建议使用 XFS 文件系统
- 内存过度分配(memory overcommit)
- 关闭 NUMA
- 禁用区域回收
- 禁用透明大内存页(THP)，透明大内存页会导致更多的磁盘IO。
- 修改限制，文件打开数、进程允许创建的线程数，通常都应该设置为无限制。
- 注意IO利用率
- 注意缺页错误率
- 注意TCP丢包
- 注意OOM killer
- 配置 oplog 大小以适合你的用例
- 确保副本集成员包含奇数个投票成员
- 不要强制关闭(`-9/SIGKILL`)，而要温柔的关闭
  - `kill -2` 或 `kill`
  - `db.shutdownServer()`

<br/>

```bash
# 内存过度分配, 建议为2
# 0 让内核来猜测过度分配的大小
# 1 内存分配总是成功
# 2 分配的虚拟地址空间最多不超过交换空间与一定过度分配比例的和
# 修改此参数无需重启MongoDB进程
echo 2 > /proc/sys/vm/overcommit_memory

# 禁用区域回收
sysctl -w vm.zone_reclaim_mode=0

# 文件打开数
ulimit -n 1000000
# 程序数
ulimit -u 500000
# 推荐的 ulimit 设置
# -f （文件大小）： unlimited
# -t （CPU时间）： unlimited
# -v（虚拟内存）：unlimited [1]
# -l （锁定的内存大小）： unlimited
# -n （打开的文件）： 64000
# -m（内存大小）：unlimited [1] [2]
# -u （进程/线程）： 64000

# 禁用透明大页
echo "never" > /sys/kernel/mm/transparent_hugepage/enabled
# 禁用透明大页碎片整理
echo "never" > /sys/kernel/mm/transparent_hugepage/defrag
```

<br/>

---

<br/>

# 日志分析

参考文档:

- [MongoDB日志浅析](https://developer.aliyun.com/article/716765)
- [MongoDB中有几种日志](https://generalthink.github.io/2019/06/27/logs-in-mongodb/)
- [MongoDB慢日志字段解析](https://cloud.tencent.com/developer/article/1711795)
- [MongoDB CPU 使用率高排查手册](https://www.volcengine.com/docs/6447/174018)

<br/>
<br/>

MongoDB 主要包括四种日志，这些日志记录着不同的信息。

- 系统日志
- Journal 日志
- Oplog 日志
- 慢查询日志

<br/>
<br/>

## 系统日志

系统日志记录了 MongoDB 的启动和停止的操作，以及服务运行过程中发生的任何异常的信息。通常，日志可用于诊断问题、监控部署和调优性能。

```yml
# 系统日志
systemLog:
  path: /var/log/mongodb/mongod.log
```

<br/>
<br/>

### 结构化日志

文档: <https://www.mongodb.com/zh-cn/docs/v6.0/reference/log-messages/>

mongod/mongos 实例以结构化 json 格式输出所有日志消息。日志条目以一系列键值对的形式编写。

<br/>
<br/>

## Journal日志

以下介绍主要基于 WiredTiger 存储引擎，它是 MongoDB 3.2 版本之后推荐的默认存储引擎。

Journaling(日记) 日志功能是 MongoDB 里非常重要的一个功能，它保证了数据库服务器在意外关机等情况下数据的完整性。Journal 日志就是预写的 redo 日志，它为 MongoDB 增加了额外的可靠性保障。除了故障恢复之外，它还可以提高写入的性能，批量提交。在这个过程中，所有的写入都可以一次提交，是单事务的（也就是全部成功或全部失败）。

不开启此功能，写入存储引擎的数据，并不会立即持久化存储，而是每一分钟做一次全量的 checkpoint，将所有数据持久化。

开启此功能，MongoDB 会在进行写入时建立一条 Journal 日志（其中包括此次写入操作具体更高的磁盘地址和字符）。因此一旦服务器突然停机，可在启动时对日记进行重放，从而重新执行那些停机之前没能够刷新到磁盘的写入操作。

MongoDB 配置 WiredTiger 引擎使用内存缓冲区来保存 journal 记录，WiredTiger 根据以下间隔或条件将缓冲的 journal 记录同步到磁盘。

- 由于 MongoDB 使用的 journal 文件大小限制为 100MB，因此 WiredTiger 大约每 100MB 数据创建一个新的 journal 日志文件。
- 从 MongoDB 3.2 版本开始，每隔 50ms 将缓冲的 journal 数据同步到磁盘。
- 如果写入操作设置了 `j: true`，则 WiredTiger 会强制同步 journal 日志文件。

上面的介绍，意味着 MongoDB 会批量地提交更改，即每次写入不会立即刷新到磁盘。不过在默认设置下，如果系统发生奔溃，不可能丢失超过 50ms 的写入数据。


向 MongoDB 中写入数据是先写入内存，然后每隔 60s 刷新到磁盘。也就是数据文件默认 60s 刷新到磁盘一次。因此 journal 日志只需要记录约 60s 的写入数据。Journal 日志系统预先分配了若干个空文件，这些文件存放在 `/dbpath/journal/_j.数字` 。数据库正常关闭后，这些文件会被清楚。

如果发生系统崩溃或使用 `kill -9` 命令强制终止数据库的运行，则 mongod 会在启动时重放 journal 日志文件，同时会显示大量的校验信息。

需要注意的是，如果客户端的写入速度超过了日志的刷新速度，mongod 则会限制写入操作，直到 journal 日志完成磁盘的写入。这是 mongod 会限制写入的唯一情况。

<br/>
<br/>

## 固定集合

在介绍 oplog 日志和慢查询日志时，需要先介绍固定集合(Capped Collection)。

MongoDB 中的普通集合是动态创建的，而且可以自动增长以容纳更多的数据。

MongoDB 中还有另一种不通类型的集合，叫做固定集合。固定集合需要事先创建好，并且它的大小是固定的。固定集合的行为与循环队列一样。如果没有空间了，最老的文档会被删除以释放新的空间，新插入的文档会占据这块空间。

固定集合创建之后就不能改变，无法将固定集合转换为非固定集合，但是可以将常规集合转换为固定集合。

```js
// 创建固定集合
// 大小为 100000 字节的固定大小集合，文档数量为 100
db.createCollection("collectionName",{"capped":true, "size":100000, "max":100});

// 将常规集合转换为固定集合
db.runCommand({"convertToCapped": "test", "size" : 10000});
```

固定集合可以进行一种特殊的排序，称为自然排序(natural sort)，自然排序返回结果集中文档的顺序就是文档在磁盘中的顺序。自然顺序就行文档在固定集合中的插入顺序。

<br/>
<br/>

## OPlog主从日志

MongoDB Replica Sets(副本集) 用于在多台服务器之间备份数据。它的复制功能是使用 oplog 实现的，操作日志包含了每一次写操作。oplog 是主节点的 local 数据库中的一个固定集合。备份节点通过查询这个集合就可以直到需要进行复制的操作。

一个 mongod 实例中的所有数据库都是用同一个 oplog，也就是所有数据库的操作日志都会记录到 oplog 中。

每个备份节点都维护者自己的 oplog，记录着每一次从主节点复制数据的操作。这样，每个成员都可以作为同步源给其他成员使用。

备份节点从当前使用的同步源中获取需要执行的操作，然后在自己的数据集上执行这些操作，最后再将这些操作写入自己的 oplog。如果遇到某个操作失败的情况（如当前同步源的数据损坏或数据不一致时），那么备份节点就会停止从当前的同步源复制数据。

oplog 中按顺序保存着所有执行过的写操作，副本集中每个成员都维护着一份自己的 oplog，每个成员的 oplog 都应该跟主节点的 oplog 完全一致（可能会有一些延迟）。

如果某个备份节点由于某些原因挂了，但它重新启动后，就会自动从 oplog 中最后一个操作开始同步。由于复制操作的过程是复制数据再写入 oplog，所以备份节点可能会在已经同步过的数据上再次执行复制操作。MongoDB 在设计之初就考虑到了这种情况：将 oplog 中的同一操作执行多次，与只执行一次的效果是一样的。

由于 oplog 是固定集合，它只能保持特定数量的操作日志。MongoDB 默认将其大小设置为可用磁盘空间的 5%（默认最小 1G，最大 50G）。可以在配置文件中首次配置 `oplogSizeMB` 设置为我们需要的值。

通常，oplog 使用空间的增长速度与系统处理写请求的速率几乎相同：如果主节点上每分钟处理了 1KB 的写入请求，那么 oplog 很可能也会在一分钟内写入 1KB 条操作日志。

如果单次请求能够影响到多个文档（比如删除/更新多个文档），oplog 中就会出现多条操作日志。如果单个操作会影响多个文档，那么每个受影响的文档都会对应 oplog 的一条日志。

<br/>

```js
# 查看 oplog
user local;
db.oplog.rs.find().sort({"ts": -1});
{
    "ts": Timestamp(1625660877, 2),
    "t": NumberLong(2),
    "h": NumberLong("5521980394145765083"),
    "v": 2,
    "op": "i",
    "ns": "test.users",
    "ui": UUID("edabbd93-76eb-42be-b54a-cdc29eb1f267"),
    "wall": ISODate("2021-07-07T12:27:57.689Z"),
    "o": {
        "_id": ObjectId("60e59dcd46db1fb4605f8b18"),
        "name": "1"
    }
}
```

```yml
# oplog 日志个字段详解
ts: 8 字节的时间戳，由4字节unix timestamp + 4字节自增计数表示。这个值很重要，在选举(如主宕机时)新主时，会选择 ts 最大的那个从作为新主
t: election term。对应raft协议里面的term，每次发生节点down掉，新节点加入，主从切换，term都会自增。
h: 操作的全局唯一id的hash结果
v: oplog 的版本字段
op: 具体操作类型, i 插入，d 删除，u 更新，c 是DDL操作(数据库命令)，n 是空消息
ns: 命名空间，即库和集合
ui: 客户端会话 id
wall: 毫秒粒度的 utc 执行时间
o: 具体的操作内容
```

<br/>
<br/>

## 慢查询日志

MongoDB 中使用系统分析器(profiler) 来查找耗时过长的操作。分析器将记录的慢日志写入 `system.profile` 固定集合中，但相应的整体性能也会有所下降。

```js
// 默认情况下，分析器处于关闭状态，不会进行任何记录。
// 0=off 1=slow 2=all
// 第一个参数指定级别，第二个参数自定义耗时过长的标准，单位毫秒
// 默认是 100ms，也就是查询超过 100ms 才会写入到系统日志中。
db.setProfilingLevel(level,<slowms>)
```

即使默认没有启用分析器，我们也可以在 mongod 实例的系统日志中看到查询日志。

<br/>

一条示例日志:

```log
"Thu Apr  2 07:51:50.985 I COMMAND  [conn541] command animal.MongoUser_58 command: find { find: \"MongoUser_58\", filter: { $and: [ { lld: { $gte: 18351 } }, { fc: { $lt: 120 } }, { _id: { $nin: [ 1244093274 ] } }, { $or: [ { rc: { $exists: false } }, { rc: { $lte: 1835400100 } } ] }, { lv: { $gte: 69 } }, { lv: { $lte: 99 } }, { cc: { $in: [ 440512, 440513, 440514, 440500, 440515, 440511, 440523, 440507 ] } } ] }, limit: 30 } planSummary: IXSCAN { lv: -1 } keysExamined:20856 docsExamined:20856 cursorExhausted:1 keyUpdates:0 writeConflicts:0 numYields:6801 nreturned:0 reslen:110 locks:{ Global: { acquireCount: { r: 13604 } }, Database: { acquireCount: { r: 6802 } }, Collection: { acquireCount: { r: 6802 } } } protocol:op_command 8938329ms"
```

```json
{
    "timestamp": "Thu Apr  2 07:51:50.985"  // 日期和时间, ISO8601格式
    "severityLevel": "I"  // 日志级别 I代表info的意思，其他的还有F,E,W,D等
    "components": "COMMAND"  //组件类别，不同组件打印出的日志带不同的标签，便于日志分类
    "namespace": "animal.MongoUser_58"  //查询的命名空间，即<databse.collection>
    "operation": "find" //操作类别，可能是[find,insert,update,remove,getmore,command]
    "command": { find: "MongoUser_58", filter: { $and: [ { lld: { $gte: 18351 } }, { fc: { $lt: 120 } }, { _id: { $nin: [1244093274 ] } }, { $or: [ { rc: { $exists: false } }, { rc: { $lte: 1835400100 } } ] }, { lv: { $gte: 69 } }, { lv: { $lte: 99 } }, { cc: { $in: [ 440512, 440513, 440514, 440500, 440515, 440511, 440523, 440507 ] } } ] }, limit: 30 } //具体的操作命令细节
    "planSummary": "IXSCAN { lv: -1 }", // 命令执行计划的简要说明，当前使用了 lv 这个字段的索引。如果是全表扫描，则是COLLSCAN
    "keysExamined": 20856, // 该项表明为了找出最终结果MongoDB搜索了索引中的多少个key
    "docsExamined": 20856, // 该项表明为了找出最终结果MongoDB搜索了多少个文档
    "cursorExhausted": 1, // 该项表明本次查询中游标耗尽的次数
    "keyUpdates":0,  // 该项表名有多少个index key在该操作中被更改，更改索引键也会有少量的性能消耗，因为数据库不单单要删除旧Key，还要插入新的Key到B-Tree索引中
    "writeConflicts":0, // 写冲突发生的数量，例如update一个正在被别的update操作的文档
    "numYields":6801, // 为了让别的操作完成而屈服的次数，一般发生在需要访问的数据尚未被完全读取到内存中，MongoDB会优先完成在内存中的操作
    "nreturned":0, // 该操作最终返回文档的数量
    "reslen":110, // 结果返回的大小，单位为bytes，该值如果过大，则需考虑limit()等方式减少输出结果
    "locks": { // 在操作中产生的锁，锁的种类有多种，如下
        Global: { acquireCount: { r: 13604 } },   //具体每一种锁请求锁的次数
        Database: { acquireCount: { r: 6802 } }, 
        Collection: { acquireCount: { r: 6802 } } 
    },
    "protocol": "op_command", //  消息的协议
    "millis" : 69132, // 从 MongoDB 操作开始到结束耗费的时间，单位为ms
}
```

<br/>

某些字段的一些值：

- severityLevels(严重级别)
- components(组件)
  - ACCESS：访问控制相关，比如认证
  - COMMAND：数据库命令，CRUD 等
  - CONTROL：控制行为，比如初始化等
  - FTDC：诊断数据收集机制相关，比如服务器统计信息和状态信息
  - GEO：与解析地理空间形状相关
  - INDEX：索引操作相关，比如创建索引
  - NETWORK：网络相关，比如链接的建立和断开
  - QUERY: 查询相关，比如查询计划
  - REPL：副本集相关，包括初始化同步、副本集节点心跳、主从同步、回滚等
    - ELECTION：副本集选举相关
    - INITSYNC：初始化同步相关
    - REPL_HB：副本集内节点心跳相关
    - ROLLBACK：回滚状态相关
  - SHARDING：分片行为相关，比如 mongos 的启动
  - STORAGE：存储相关
  - RECOVERY：恢复状态相关
  - JOURNAL：journal相关
  - TXN：多文档事务相关
  - 其他
- operation(操作类别)
  - find
  - insert
  - delete
  - replace
  - update
  - drop
  - renmae
  - dropDatabse
- writeConflicts(写冲突次数)：写是要加写锁的，如果写冲突次数很多，比如多个操作同时更新同一个文档，可能会导致该操作耗时较长，主要就消耗在写操作这里。
- planSummary(执行计划)
  - COLLSCAN：全表扫描，考虑添加相应的索引或优化查询语句
  - IXSCAN：索引扫描，正常情况下一般是它
  - IDHACK：使用默认的 `_id` 索引
  - FETCH：根据索引去检索某一个文档
  - SHARD_METGE：将各个分片的返回数据进行聚合
  - SHARDING_FILTER：通过 mongos 对分片数据进行查询
- yield(屈服)：就是让出锁的意思
- locks(锁)
  - global(全局维度)
  - Database(库维度)
  - Collection(集合维度)

<br/>
<br/>

## CPU使用率高排查

若存在查询语句不够优化（如未设置合理的索引）、并发请求量大、计算任务过重时，可能会使 CPU 满载，从而导致数据读写变慢、超时增加等问题。

<br/>
<br/>

### 查看正在运行的语句

执行 `db.currentOp()` 命令查看数据库正在运行的语句。

在返回结果中，需要重点关注以下字段:

- client：发起请求的客户端。
- opid：当前操作的标识符，可通过 `db.killOp(opid)` 命令来终止操作。
- secs_running：当前操作的持续时间，单位秒。如果操作持续时间较长，建议您查看请求是否合理。
- microsecs_running：单位毫秒。
- ns：命名空间
- op：当前操作的类型。
- locks：与锁相关的信息。

<br/>
<br/>

### 查看慢日志

下表列举了慢日志中部分关键字以及导致慢查询的原因，并提供了一些处理建议。

| 导致慢查询出现的原因 | 关键字<img width=250/> | 处理建议 |
| - | - | - |
| 执行了全表扫描 | `COLLSCAN` <br/> `docsExamined` | 如果慢日志中的请求出现了 `COLLSCAN` 关键字，表示这些请求执行了全表扫描，全表扫描会非常占用大量 CPU 资源。如果这些请求比较频繁，您可以对查询的字段建立索引来优化。 <br/> 您可以通过 `docsExamined` 字段值，帮助确认当前查询请求扫描了多少文档，该值越大，表示当前请求所占用的 CPU 越多。 |
| 索引使用不合理 | `IXSCAN` <br> `keysExamined` | 如果慢日志中出现了`IXSCAN` 关键字，表示该请求使用了索引。 <br> 您可以进一步查看 `keysExamined` 字段值，帮助确认当前请求扫描了多少条索引。该值越大，表示 CPU 占用越多。 <br/> 索引建立的是否合理，对查询的请求开销和执行速度影响很大。索引不是越多越好，索引过多会影响写入、更新的性能。如果您的应用偏向于写操作，索引可能会影响性能。建议您根据业务特点建立合理的索引。|
| 存在大量数据数据排序 | `SORT` <br> `hasSortStage` | 如果在慢日志中出现了`SORT` 关键字，您可以考虑通过索引来优化排序。<br/> 当查询请求中的 `hasSortStage` 字段为 `true` 时，表示当前请求中存在排序。 <br> 如果排序无法通过索引满足，MongoDB 会在查询结果中进行排序，而排序操作会消耗大量 CPU 资源，这种情况下，您可以对需要经常排序的字段建立索引，来优化查询，减少 CPU 资源的占用。|

<br/>
<br/>

### 分析执行计划

MongoDB 提供了 `explain()` 命令来查看指定查询的查询计划统计信息，例如所用的索引、查询语句能否被索引覆盖、所扫描的索引项数量、所读取的文档数量、所返回的文档数量、执行查询所需的时间等信息。

您可以通过查询计划中的上述信息帮助建立合适的索引，来优化查询从而减少 CPU 资源消耗。

<br/>
<br/>

### 使用与业务负载相匹配的实例配置

根据数据库正在运行的语句、慢请求以及执行计划的分析结果，对数据库请求使用了合理索引后， 如果 CPU 使用率高的问题仍然存在，那么您还需要评估 MongoDB 实例的当前配置是否能够满足业务需求。


<br/>

---

<br/>

# mtools工具

参考:

- [mtools](https://github.com/rueckstiess/mtools)
- [mtools docs](https://rueckstiess.github.io/mtools/)

<br/>

mtools 是一个辅助脚本的工具集，用于解析、过滤和可视化 MongoDB 日志文件，也可以在本地机器上快速地建立 MongoDB 测试环境，以及在 MongoDB 实例之间传输数据库的工具。

mtools 工具集包含以下工具：

- mlogfilter：按时间切分日志文件，合并日志文件，过滤慢查询，查找全表扫描，缩短日志行，按其他属性过滤，转换为 JSON 格式。
- mloginfo：返回有关日志文件的信息，如开始和结束时间、版本、二进制，特殊部分如重启、连接、试图等。
- mplotqueries：可视化日志文件。
- mlaunch：用于快速启动本地测试环境，包括复制集和分片。
- mtransfer：一个实验性的脚本，通过复制 WiredTiger 数据文件在 MongoDB 实例之间转移数据库。

<br/>
<br/>

## 安装mtools

mtools 工具集使用 Python 编写，它只对 MongoDB 服务器的有效版本进行测试。

```bash
pip3 install mtools
```

<br/>
<br/>

## mlaunch工具

mlaunch 工具在本地快速启动和监控 MongoDB 环境。它支持单节点、副本集和分片集群的各种配置。

<br/>
<br/>

## mlogfilter工具

mlogfilter 是一个用于减少 MongoDB 日志文件的信息量的脚本，它将 MongoDB 日志文件作为输入，加上一些过滤参数，解析包含的日志行，并根据过滤参数输出匹配的行。

```sh
# 用法
mlogfilter mongod.log

# 或通过 pipe
tail -f mongod.log | mlogfilter [parameters]
```

一些有用的参数

```bash
# 把长行缩短为指定字符，默认为200
--shorten [LENGTH]

# 人类可读
--human

# 排除所有匹配过滤器的行
--exclude

# json 格式输出，可与 mongoimport 命令结合使用，将日志文件存储到 MongoDB 数据库中。
--json
# mlogfilter mongod.log --slow --json | mongoimport -d test -c mycoll

# 时间戳格式 iso8601-utc, iso8601-local
--timestamp-format FORMAT

# 指定命名空间, <database>.<collection>
--namespace admin.\$cmd

# 指定操作, query, insert, update, delete, command, getmore
--operation OP

# 按线程名称过滤
--thread conn1234

# 按模式过滤
--pattern '{"_id": 1, "host": 1, "ns": 1}'

# 按大于/小于 执行时间过滤
--slow MS
--fast MS

# 按未使用索引的全表扫描过滤
--scan

# 按关键字过滤
--word assert warning error

# 按时间片过滤
--from FROM [FROM ...], --to TO [TO ...]
```

<br/>
<br/>

## mongoinfo工具

mongoinfo 报告日志文件的默认信息。

```sh
# 用法
mloginfo mongod.log

     source: mongod.log
       host: enter.local:27019
      start: 2017 Dec 14 05:56:48.578
        end: 2017 Dec 14 05:57:55.965
date format: iso8601-local
     length: 190
     binary: mongod
    version: 3.4.9
    storage: wiredTiger
```

一些有用的参数：

```sh
# 收集每个查询模式的统计信息
--queries
# 根据查询排序
# 有效的排序项: ns, pattern, count, min, max, mean, 95% and sum.
--queries --sort count

# 查看重启信息
--restarts

# 根据消息类型将所有行分组
--distinct

# 打开和关闭的连接数信息
--connections

# 副本集状态变化信息
--rsstate

# 事务信息
--transactions

# 游标信息
--cursors

# 有关慢事务的存储信息
--storagestats

# 分片信息
--sharding
```

<br/>
<br/>

## mplotqueries工具

mplotqueries 是一个可视化 MongoDB 日志文件的工具。

<br/>
<br/>

## mtransfer工具

mtransfer 工具允许 WiredTiger 数据库从一个 MongoDB 实例导出并导入到另一个实例中。

<br/>

---

<br/>

# 参考信息

<br/>
<br/>

## 错误代码

发生错误时，MongoDB 将返回以下代码之一。

错误码：<https://www.mongodb.com/zh-cn/docs/v6.0/reference/error-codes/>

<br/>
<br/>

## 限制和阈值

MongoDB 系统的硬件限制和软限制。

<br/>
<br/>

### BSON文档

BSON 文档的大小限制为 16MB。确保单个文档不会使用过多的内存，或传输过程中不会使用过多的带宽。MongoDB 提供了 GridFS API 来存储超过最大大小的文档。

BSON 文档的嵌套级别不超过 100 个。每个对象或数组都会添加一个级别。

<br/>
<br/>

### 命名限制

数据库区分大小写，请勿依靠大小写来区分数据库。

Windows 数据库名称不得包含以下字符：`/\. "$*<>:|?`。Unix 和 Linux 上名称不能包含 `/\. "$`。 名称还不能包含 null 字符。

数据库名称长度不能为空且必须小于 64 字节。

集合名称应以下划线或字母开头，并且不能：

- 为空
- 包含 `$`
- 包含 null 字符
- 以 `system.` 前缀开头

未分片集合和视图的命名空间长度限制为 255 字节，分片集合的命名空间长度限制为 235 字节。对于集合或视图，命名空间包括 `db.collection`。

字段名称不能包含 null 字符，允许包含 `.` 和 `$` 符号。

字段 `_id` 保留用作主键，其值在集合中必须是唯一的、不可变的，并且可以是除数组或正则之外的任何类型。如果它包含子字段，则子字段名称不能以 `$` 符号开头。

<br/>
<br/>

### 命名警告

请注意，本节讨论的问题可能会导致数据丢失或损坏。

不支持重复的字段名称。

带 `$` 和 `.` 字符的字段名称，`mongoimport` 和 `mongoexport` 在某些情况下可能无法按预期运行。在插入和更新时，驱动程序可能会有问题，也有可能导致数据丢失。

<br/>
<br/>

### 索引限制

每个集合最多可以有 64 个索引。

复合索引中字段数量不能超过 32 个。

查询无法同时使用文本索引和地理空间索引。

具有 2dsphere 索引的字段只能保存几何图形。2dsphere 索引键的有限数量。

WiredTiger 存储引擎从涵盖查询返回的 NaN 值始终为双精度类型。

多键索引的键索引多。

地理空间索引不能覆盖查询。

索引构建中的内存使用情况。`createIndexes` 使用磁盘上的内存和临时文件的组合来完成索引构建，它默认的内存使用限制为 200 MB，内存在使用单个 `createIndexes` 命令构建的所有索引之间共享。达到内存限制后，它将使用 `_tmp` 目录中名为 `--dbpath` 的子目录中的临时磁盘文件来完成构建。

可以通过设置 `maxIndexBuildMemoryUsageMegabytes` 参数来覆盖内存限制。设置更高的值可能会加快索引构建过程。但设置太高可能导致内存耗尽。

以下索引只支持简单的二进制比较，不支持排序规则：文本索引、2d 索引和 geoHaystack 索引。

无法隐藏 `_id` 索引。

<br/>
<br/>

### 排序限制

排序键的最大数量是 32 个键。

<br/>
<br/>

### 数据限制

固定大小集合的最大文档数，如果指定，必须小于 $2^{31}$ 个文档。如果未指定最大文档数，则没有限制。

<br/>
<br/>

### 副本集限制

副本集最多可以有 50 个节点。

一个副本集最多可以有 7 个投票成员。对于成员数量超过 7 的副本集，请设置无投票权的成员。

如果没有显式指定 oplog 大小，MongoDB 将创建一个不大于 50GB 的 oplog。

<br/>
<br/>

### 分片集群限制

分片环境中的不可用操作：不支持 `geoSearch` 命令，`$where` 不允许从 `db` 函数引用 `$where` 对象。

在 mongos 上运行时，如果索引包含分片键，则索引只能覆盖对分片集合的查询。

MongoDB 不支持跨分片的唯一索引，除非唯一索引包含完整分片键作为索引的前缀。在这些情况下，将在整个键上强制执行唯一性，而不是单个字段。

默认情况下，如果一个范围中的文档数大于配置的范围大小除以平均文档大小的结果的 2倍，则 MongoDB 无法移动该范围。`db.collection.status()` 中 `avgObjSize` 字段，表示集合中的平均文档大小。

分片键索引可为分片键的升序索引、以分片键开头并指定分片键升序的复合索引或是哈希索引。

分片键索引不能为：

- 分片键的降序索引
- 部分索引
- 以下任何类型：地址空间、多键、文本、通配符。

分片键的选择，从 MongoDB 5.0 开始，你可以通过更改文档的分片键对集合重新分片。可以通过向现有分片键添加后缀字段或字段来优化分片键。

对于插入量较高的集群，具有单调递增和递减键的分片键可能会影响插入吞吐量。当使用单调递增的分片键插入文档时，所有插入内容都属于单个分片上的同一个数据段。系统最终会划分接收所有写入操作的数据段范围并迁移其内容，从而更均匀地分布数据。然而，集群数据只将插入操作定向到单个分片，这会造成插入吞吐量瓶颈。

要避免此约束，请使用哈希分片键或选择不单调增加或减少的字段。哈希分片键和哈希索引存储具有升序值的键的哈希值。

<br/>
<br/>

### 操作限制

如果 MongoDB 无法使用一个或多个索引来获取排序顺序，则它必须对数据执行阻塞排序操作。阻塞是指要求 `SORT` 阶段在返回任何输出文档之前读取所有输入文档，从而阻止改特定查询的数据流。

如果 MongoDB 需要使用超过 100MB 的系统内存进行阻塞排序操作，则它将返回错误，除非查询指定了 `allowDiskUse()` 允许使用磁盘上的临时文件来存储超过 100MB 系统内存限制的数据。

对于多文档事务：

- 可在事务中创建集合和索引。
- 事务中使用的集合可以位于不同的数据库中。
- 无法在跨分片事务中创建新集合。
- 不能写入固定大小集合。
- 从集合读取时不能使用读关注。
- 不能在 config, admin 或 local 库中读写集合。
- 不能写入 `system.*` 集合。
- 不能使用 `explain` 执行查询计划。
- 对于在 ACID 事务外部创建的游标，无法在 ACID 事务内部调用 `getMore`。
- 对于在事务中创建的游标，无法在事务外部调用 `getMore`。
- 不能将 `killCursors` 指定为事务。

<br/>
<br/>

### 会话限制

会话空闲超时，连续 30 分钟未收到读写操作或未使用 `refreshSessions` 进行刷新的会话将被标记为已过期，并可由 MongoDB Server 随时将其关闭。关闭会话时会终止所有进行中的操作并打开与该会话关联的游标。

<br/>
<br/>

## 客户端方法

`mongo` shell 在 MongoDB v5.0 中已被弃用，替换为 `mongosh`。

尽管这些方法使用 JavaScript，但与 MongoDB 的大多数交互并不使用 JavaScript，而是使用交互程序语言中的惯用驱动程序。

<br/>
<br/>

### 集合方法

使用集合方法 `db.collection.method()`。

| 方法名称 | 说明 |
| - | - |
| `aggregate()` | 提供聚合管道 |
| `bulkWrite()` | 提供批量写入操作 |
| `count()` | 返回集合或视图中文档的计数 |
| `countDocuments()` | 使用 `$sum` 表达式和 `$group` 聚合阶段，返回集合或视图中文档的计数 |
| `createIndex()` | 在集合上创建索引 |
| `createIndexes()` | 为集合创建一个或多个索引 |
| `dataSize()` | 返回集合的大小 |
| `deleteOne()` | 删除集合中的单个文档 |
| `deleteMany()` | 删除集合中的多个字段 |
| `distinct()` | 返回具有指定字段的不同值的文档数组 |
| `drop()` | 从数据库中删除指定的集合 |
| `dropIndex()` | 删除集合中的指定索引 |
| `dropIndexes()` | 删除集合上的所有索引 |
| `explain()` | 返回各种方法的查询执行信息 |
| `find()` | 对集合或视图执行查询，并返回游标对象 |
| `findAndModify()` | 以原子方式修改并返回单个文档 |
| `findOne()` | 执行查询并返回单个文档 |
| `findOneAndDelete()` | 查找并删除单个文档 |
| `findOneAndReplace()` | 查找并替换单个文档 |
| `findOneAndUpdate()` | 查找并更新单个文档 |
| `getIndexes()` | 返回说明集合上现有索引的一组文档 |
| `getShardDistribution()` | 报告分片集群中数据段分布的数据 |
| `getShardVersion()` | 分片集群的内部诊断方法 |
| `hideIndex()` | 在查询规划器中隐藏索引 |
| `insertOne()` | 将新文档插入集合 |
| `insertMany()` | 将多份文档插入集合 |
| `isCapped()` | 报告集合是否为固定大小集合 |
| `latencyStats()` | 返回集合的延迟统计信息 |
| `mapReduce()` | 执行 map-reduce 样式的数据聚合 |
| `reIndex()` | 重新构建集合上的所有现有索引 |
| `remove()` | 从集合删除文档 |
| `renameCollection()` | 更改集合的名称 |
| `replaceOne()` | 替换集合中的单个文档 |
| `stats()` | 报告集合的状态 |
| `storageSize()` | 报告集合使用的总大小（以字节为单位）|
| `totalIndexSize()` | 报告集合上索引使用的总大小 |
| `totalSize()` | 报告集合的总大小，其中包括集合中所有文档和所有索引的大小 |
| `unhideIndex()` | 从查询规划器中取消隐藏索引 |
| `updateOne()` | 修改集合中的单个文档 |
| `updateMany()` | 修改集合中的多个文档 |
| `watch()` | 在集合上建立变更流 |
| `validate()` | 对集合执行诊断操作 |

<br/>
<br/>

### 游标方法

使用游标方法：`cursor.method()`。

| 方法名称 | 说明 |
| - | - |
| `addOption()` | 添加可修改查询行为的特殊传输协议标志 |
| `allowDiskUse()` | 允许在处理阻塞排序操作时使用磁盘上的临时文件来存储超过 100 MB 系统内存限制的数据 |
| `allowPartialResults()` | 如果一个或多个查询的分片不可用，则允许对分片集合进行 find 操作，以返回部分结果，而不是错误 |
| `batchSize()` | 控制将在单个网络消息中返回到客户端的文档数量 |
| `close()` | 关闭游标并释放关联的服务器资源 |
| `isClosed()` | 游标是否关闭 |
| `collation()` | 指定 `db.collection.find()` |
| `comment()` | 将注释附加到查询，以允许在日志和 system.profile 集合中进行跟踪 |
| `count()` | 修改游标，以返回结果集中的文档数量，而不是文档本身 |
| `explain()` | 报告游标的查询执行计划 |
| `forEach()` | 对游标中的每个文档应用 JavaScript 函数 |
| `hasNext()` | 游标是否包含文档且可迭代 |
| `hint()` | 强制对查询使用特定索引 |
| `isExhausted()` | 游标是否已关闭并批处理中没有剩余对象 |
| `itcount()` | 通过获取和迭代结果集，计算游标客户端中的文档总数 |
| `limit()` | 限制游标结果集的大小 |
| `map()` | 将函数应用于游标中的每个文档，并收集数组中的返回值 |
| `max()` | 指定游标的独占索引上限 |
| `maxTimeMS()` | 指定在游标上处理操作的累计时间限制（毫秒）|
| `min()` | 指定游标的包含式索引下限 |
| `next()` | 返回游标中的下一个文档 |
| `noCursorTimeout()` | 指示服务器避免在一段时间不活动后自动关闭游标 |
| `objsLeftInBatch()` | 返回当前游标批处理中剩余文档的数量 |
| `pretty()` | 将游标配置为以易于阅读的格式显示结果 |
| `readConcern()` | 为游标指定读关注 |
| `readPref()` | 为游标指定读偏好 |
| `returnKey()` | 修改游标以返回索引键而不是文档 |
| `showRecordId()` | 向游标返回的每个文档添加内部存储引擎 ID 字段 |
| `size()` | 返回游标中文档的计数 |
| `skip()` | 返回一个游标，该游标仅在传递或跳过多个文档后才开始返回结果 |
| `sort()` | 根据排序规范返回排序的结果 |
| `tailable()` | 将游标标记为循环式。仅对超过固定大小集合的游标有效 |
| `toArray()` | 返回一个数组，其中包含游标返回的所有文档 |

<br/>
<br/>

### 数据库方法

数据库方法使用：`db.method()`。

| 方法名称 | 说明 |
| - | - |
| `adminCommand()` | 对 admin 库运行命令 |
| `aggregate()` | 运行不需要底层集合的管理/诊断管道 |
| `commandHelp()` | 返回数据库命令 |
| `createCollection()` | 创建新的集合或视图。通常用于创建固定大小集合 |
| `createView()` | 创建视图 |
| `currentOp()` | 报告当前正在进行的操作 |
| `dropDatabase()` | 删除当前数据库，慎重！|
| `fsyncLock()` | 刷新写入磁盘并锁定数据库，以防止写入操作并协助备份操作 |
| `fsyncUnlock()` | 解锁数据库 |
| `getCollection()` | 返回集合或视图对象 |
| `getCollectionInfos()` | 返回当前数据库中所有集合和视图的集合信息 |
| `getCollectionNames()` | 列出当前数据库中的所有集合和视图 |
| `getLogComponents()` | 返回该日志消息详细程度 |
| `getMongo()` | 返回当前连接的 Mongo 连接对象 |
| `getName()` | 返回当前数据库的名称 |
| `getProfilingStatus()` | 返回反映当前分析级别和分析阈值的文档 |
| `getReplicationInfo()` | 返回包含复制统计数据的文档 |
| `getSiblingDB()` | 提供对指定数据库的访问权限 |
| `hello()` | 返回报告副本集状态的文档 |
| `help()` | 帮助信息 |
| `hostInfo()` | 系统信息 |
| `killOp()` | 终止指定的操作 |
| `listCommands()` | 显示常用数据库命令列表 |
| `printCollectionStats()` | 打印每个集合的统计信息 |
| `printReplicationInfo()` | 从主节点的角度打印副本集的状态报告 |
| `printSecondaryReplicationInfo()` | 从从节点的角度打印副本集的状态 |
| `printShardingStatus()` | 打印分片配置和数据段范围的报告 |
| `rotateCertificates()` | 执行在线 TLS 证书轮换 |
| `runCommand()` | 运行数据库命令 |
| `serverBuildInfo()` | 返回显示 mongod 实例的编译参数的文档 |
| `serverCmdLineOpts()` | 返回一个文档，其中包含有关用于启动 MongoDB 实例的运行时的信息 |
| `serverStatus()` | 返回体现数据库进程状态概况的文档 |
| `setLogLevel()` | 设置单个日志消息详细级别 |
| `setProfilingLevel()` | 修改数据库分析的当前级别 |
| `shutdownServer()` | 安全彻底地关闭当前实例 |
| `stats()` | 返回报告当前数据库状态的文档 |
| `version()` | 返回实例的版本 |
| `watch()` | 打开变更流游标，以便数据库报告其所有非 system 集合的情况。无法在 admin、local 或 config 数据库中打开 |

<br/>
<br/>

### 查询计划缓存方法

查询计划缓存方法使用：`PlanCache.method()`。

| 方法名称 | 说明 |
| - | - |
| `db.collection.getPlanCache()` | 返回一个接口，用于访问查询计划缓存对象和集合的相关 PlanCache 方法 |
| `PlanCache.clear()` | 清除集合的所有缓存查询计划 |
| `clearPlansByQuery()` | 清除指定查询结构的缓存查询计划 |
| `list()` | 返回集合的计划缓存信息 |

<br/>
<br/>

### 批量写入操作方法

批量写入操作方法使用：`Bulk.method()`。

| 方法名称 | 说明 |
| - | - |
| `db.collection.initializeOrderedBulkOp()` | 为有序的操作列表初始化 `Bulk()` 操作构建器 |
| `db.collection.initializeUnorderedBulkOp()` | 为无序的操作列表初始化 `Bulk()` 操作构建器 |
| `Bulk()` | 批量操作构建器 |
| `execute()` | 批量执行操作列表 |
| `find()` | 指定更新或删除操作的查询条件 |
| `find.arrayFilters()` | 指定筛选器，确定要为 update 或 updateOne 操作更新数组的哪些元素 |
| `find.collation()` | 指定查询条件的排序规则 |
| `find.delete()` | 将多文档删除操作添加到操作列表中 |
| `find.deleteOne()` | 将单个文档删除操作添加到操作列表中 |
| `find.hint()` | 指定用于更新/替换操作的索引 |
| `find.replaceOne()` | 将单个文档替换操作添加到操作列表中 |
| `find.updateOne()` | 将单文档更新操作添加到操作列表中 |
| `find.update()` | 将多个更新操作添加到操作列表中 |
| `find.upsert()` | 为更新操作指定 `upsert: true` |
| `getOperations()` | 返回在 Bulk 操作对象中执行的写入操作数组 |
| `insert()` | 将插入操作添加到操作列表中 |
| `toJSON()` | 返回 JSON 文档，其中包含 Bulk 操作对象中的操作和批次数 |
| `toString()` | 以字符串形式返回 toJSON 结果 |

<br/>
<br/>

### 用户管理方法

用户管理方法使用：`db.method()`。

| 方法名称 | 说明 |
| - | - |
| `auth()` | 验证数据库的用户身份 |
| `changeUserPassword()` | 更改现有用户的密码 |
| `createUser()` | 创建新用户 |
| `dropUser()` | 删除单个用户 |
| `dropAllUsers()` | 删除与数据库关联的所有用户 |
| `getUser()` | 返回指定用户的信息 |
| `getUsers()` | 返回与数据库关联的所有用户的信息 |
| `grantRolesToUser()` | 向用户分配角色及其特权 |
| `revokeRolesFromUser()` | 从用户中删除角色 |
| `updateUser()` | 更新用户数据 |

<br/>
<br/>

### 角色管理方法

角色管理方法使用：`db.method()`。

| 方法名称 | 说明 |
| - | - |
| `createRole()` | 创建角色并指定其特权 |
| `dropRole()` | 删除用户定义的角色 |
| `dropAllRoles()` | 删除与数据库关联的所有用户定义的角色 |
| `getRole()` | 返回指定角色的信息 |
| `getRoles()` | 返回数据库中所有用户定义角色的信息 |
| `grantPrivilegesToRole()` | 为用户定义的角色分配特权 |
| `revokePrivilegesFromRole()` | 从用户定义的角色中删除指定特权 |
| `grantRolesToRole()` | 指定用户定义角色继承相关特权的角色 |
| `revokeRolesFromRole()` | 从角色中删除继承的角色 |
| `updateRole()` | 更新用户定义的角色 |

<br/>
<br/>

### 副本集方法

副本集方法使用：`rs.method()`。

| 方法名称 | 说明 |
| - | - |
| `add()` | 将节点添加到副本集 |
| `addArb()` | 将仲裁节点添加到副本集 |
| `conf()` | 返回副本集配置文档 |
| `freeze()` | 阻止当前节点在一段时间内寻求选举为主节点 |
| `help()` | 帮助信息 |
| `initiate()` | 初始化新的副本集 |
| `isMaster()` | 是否为主节点 |
| `printReplicationInfo()` | 从主节点的角度打印副本集状态的格式化报告 |
| `printSecondaryReplicationInfo()` | 从从节点的角度打印副本集状态的格式化报告 |
| `reconfig()` | 通过应用新的副本集配置对象来重新配置副本集 |
| `remove()` | 从副本集中删除节点 |
| `secondaryOk()` | 允许在从节点上查询数据 |
| `status()` | 返回包含副本集状态信息的文档 |
| `stepDown()` | 导致当前的主节点变为强制选举 |
| `syncFrom()` | 设置该副本集节点与哪个节点进行同步，复写默认的同步目标选择逻辑 |

<br/>
<br/>

### 分片方法

分片方法使用：`sh.method()`。

| 方法名称 | 说明 |
| - | - |
| `abortReshardCollection()` | 中止重新分片操作 |
| `addShard()` | 将分片添加到分片集群 |
| `addShardTag()` | 将分片与标签相关联 |
| `addShardToZone()` | 将分片与区域关联 |
| `addTagRange()` | 将一系列分片键值附加到使用 addShardTag 方法创建的分片标签 |
| `balancerCollectionStatus()` | 返回有关分片集合的数据段是否均衡的信息 |
| `commitReshardCollection()` | 强制重分区操作以阻止写入并完成 |
| `disableBalancing()` | 禁用分片数据库中单个集合的均衡。不影响分片集群中其他集合的均衡 |
| `enableBalancing()` | 启用单个集合的均衡 |
| `disableAutoSplit()` | 禁用分片集群自动分割 |
| `enableAutoSplit()` | 启用分片集群自动分割 |
| `enableSharding()` | 创建数据库，在数据库上启用分片 |
| `getBalancerState()` | 是否启用均衡器 |
| `getShardedDataDistribution()` | 返回分片集合的数据分布信息 |
| `getShouldAutoSplit()` | 是否启用自动分割 |
| `help()` | 帮助信息 |
| `isBalancerRunning()` | 返回均衡器状态 |
| `moveChunk()` | 迁移分片集群中的数据段 |
| `removeTagRange()` | 从定义的分片键值范围中删除指定的分片标签 |
| `removeRangeFromZone()` | 删除一系列分片键值与区域之间的关联 |
| `removeShardTag()` | 删除标签和分片之间的关联 |
| `removeShardFromZone()` | 删除分片与区域之间的关联 |
| `reshardCollection()` | 启动重新分片操作以更改集合的分片键，从而更改数据的分布 |
| `setBalancerState()` | 启用或禁用在分片之间迁移数据段的均衡器 |
| `shardCollection()` | 为集合启用分片 |
| `splitAt()` | 使用分片键的特定值作为分界点，将现有数据段分成两个数据段 |
| `splitFind()` | 将包含与查询匹配的文档的现有数据段分成两个大致相等的数据段 |
| `startBalancer()` | 启用均衡器并等待均衡开始 |
| `status()` | 分片集群状态 |
| `stopBalancer()` | 禁用均衡器并等待任何正在进行的均衡轮次完成 |
| `waitForBalancer()` | 内部。等待均衡器状态发生变化 |
| `waitForBalancerOff()` | 内部。等待均衡器停止运行 |
| `waitForPingChange()` | 内部。等待分片集群中一个 mongos 的 ping 状态发生变化 |
| `updateZoneKeyRange()` | 将一系列分片键与一个区域关联 |

<br/>
<br/>

### 构造函数

| 方法名称 | 说明 |
| - | - |
| `BinData()` | 返回二进制数据对象 |
| `BulkWriteResult()` | 封装器，包含 `Bulk.execute()` 方法的结果 |
| `Date()` | 创建日期对象。默认情况下，创建一个包含当前日期的日期对象 |
| `ObjectId()` | 返回对象标识符 |
| `ObjectId.getTimestamp()` | 返回对象标识符 |
| `ObjectId.toString()` | 返回对象标识符 |
| `ObjectId.valueOf()` | 以十六进制字符串形式显示对象标识符的 str 属性 |
| `UUID()` | 将 32 字节十六进制字符串转换为 UUID BSON 子类型 |
| `WriteResult()` | 来自写入方法的副本集的封装器 |
| `WriteResult.hasWriteError()` |
| `WriteResult.hasWriteConcernError()` |

<br/>
<br/>

### 连接方法

| 方法名称 | 说明 |
| - | - |
| `connect()` | 连接到实例上的指定数据库 |
| `Mongo()` | 创建新连接对象 |
| `Mongo.getDB()` | 返回数据库对象 |
| `Mongo.getReadPrefMode()` | 返回连接的当前读取偏好模式 |
| `Mongo.getReadPrefTagSet()` | 返回为该连接设置的读取偏好标签集 |
| `Mongo.setCausalConsistency()` | 启用或禁用连接对象的因果一致性 |
| `Mongo.setReadPref()` | 设置该连接的读取偏好 |
| `Mongo.startSession()` | 对该连接对象启动会话 |
| `Mongo.watch()` | 打开部署的变更流游标，以报告其所有数据库中的所有非 system 集合，不包括内部 admin、local 和 config 数据库 |
| `Session()` | 会话对象 |
| `SessionOptions()` | 会话的选项对象 |

<br/>
<br/>

## 操作符

查询、更新、投影和聚合框架操作符的文档。

<br/>
<br/>

### 查询操作符

比较操作符

| 名称 | 说明 |
| - | - |
| `$eq` | 等于 |
| `$gt` | 大于 |
| `$gte` | 大于等于 |
| `$in` | 匹配数组中指定的值 |
| `$lt` | 小于 |
| `$lte` | 小于等于 |
| `$ne` | 不等于 |
| `$nin` | 不匹配数组中指定的值 |

<br/>

逻辑操作符

| 名称 | 说明 |
| - | - |
| `$and` | 返回与两个子句的条件匹配的所有文档 |
| `$not` | 不匹配的文档 |
| `$nor` | 返回无法匹配这两个子句的所有文档 |
| `$or` | 返回符合任一子句条件的所有文档 |

<br/>

元素操作符

| 名称 | 说明 |
| - | - |
| `$exists` | 匹配具有指定字段的文档 |
| `$type` | 如果字段为指定类型，则选择文档 |

<br/>

评估操作符

| 名称 | 说明 |
| - | - |
| `$expr` | 允许在查询语言中使用聚合表达式 |
| `$jsonSchema` | 根据给定的 JSON 模式验证文档 |
| `$mod` | 对字段值执行模运算，并选择具有指定结果的文档 |
| `$regex` | 选择值匹配指定正则表达式的文档 |
| `$text` | 执行文本搜索 |
| `$where` | 匹配满足 js 表达式的文档 |

<br/>

数组操作符

| 名称 | 说明 |
| - | - |
| `$all` | 匹配包含查询中指定的所有元素的数组 |
| `$elemMatch` | 如果数组字段中的元素与所有指定的条件均匹配，则选择文档 |
| `$size` | 如果数组字段达到指定大小，则选择文档 |

<br/>

其他查询操作符

| 名称 | 说明 |
| - | - |
| `$comment` | 为查询谓词添加注释 |
| `$rand` | 生成介于 0 和 1 之间的随机浮点数 |
| `$natural` | 可通过 sort 和 hint 方法提供的特殊提示，用于强制执行正向或反向集合扫描 |

<br/>
<br/>

### 更新操作符

语法：

```js
{
  <operator1>: { <field1>: <value1>, ...},
  <operator2>: { <field2>: <value2>, ...},
  ...
}
```

<br/>

字段更新运算符

| 名称 | 说明 |
| - | - |
| `$currentDate` | 将字段的值设置为当前日期 |
| `$inc` | 将字段的值按指定量递增 |
| `$min` | 仅当指定值小于现有字段值时才更新字段 |
| `$max` | 仅当指定值大于现有字段值时才更新字段 |
| `$mul` | 将字段的值乘以指定量 |
| `$rename` | 重命名字段 |
| `$set` | 设置文档中字段的值 |
| `$setOnInsert` | 如果某一更新操作导致插入文档，则设置字段的值 |
| `$unset` | 从文档中删除指定的字段 |

<br/>

数组更新运算符

| 名称 | 说明 |
| - | - |
| `$` | 充当占位符，用于更新与查询条件匹配的第一个元素 |
| `$[]` | 充当占位符，以更新数组中与查询条件匹配的文档中的所有元素 |
| `$[<identifier>]` | 充当占位符，以更新与查询条件匹配的文档中所有符合条件的元素 |
| `$addToSet` | 仅向数组中添加尚不存在于该数组的元素 |
| `$pop` | 删除数组的第一项或最后一项 |
| `$pull` | 删除与指定查询匹配的所有数组元素 |
| `$push` | 向数组添加一项 |
| `$pushAll` | 从数组中删除所有匹配值 |
| `$each` | 修改 push 和 addToSet 运算符，以在数组更新时追加多个项目 |
| `$position` | 修改 push 运算符，以指定在数组中添加元素的位置 |
| `$slice` | 修改 push 运算符以限制更新后数组的大小 |
| `$sort` | 修改 push 运算符，以对存储在数组中的文档重新排序 |

<br/>

按位更新运算符

| 名称 | 说明 |
| - | - |
| `$bit` | 对整数值执行按位 AND、OR 和 XOR 更新 |

<br/>
<br/>

### 聚合管道阶段

文档: <https://www.mongodb.com/zh-cn/docs/v6.0/reference/operator/aggregation-pipeline/>

在 `aggregate()` 方法中，管道阶段出现在数组中。

