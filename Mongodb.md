# MongoDB

## find

find = select

find 返回的是游标

![image-20220107153337882](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220107153337882.png)

![image-20220107153429208](D:\Typora文档\Java Basic\pic\image-20220107153429208.png)

![image-20220107153546181](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220107153546181.png)

![image-20220110150037308](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220110150037308.png)

![image-20220110150112029](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220110150112029.png)

聚合查询

![image-20220110150205051](D:\Typora文档\JavaBasic\pic\image-20220110150205051.png)

![image-20220110150344711](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220110150344711.png)

复杂mongo查询

![image-20220110151137720](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220110151137720.png)

$unwind json数组展开

​	

![image-20220110151232607](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220110151232607.png)



group:

_id:null 表示所有字段，也可以按某一集合来处理

![image-20220110153136602](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220110153136602.png)

复杂聚合操作

![image-20220110154016105](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220110154016105.png)

mongodb 复制集

![image-20220110155430970](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220110155430970.png)

复制集如何操作

![image-20220110155811686](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220110155811686.png)

mongoDB 高可用选举

![image-20220110155932040](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220110155932040.png)

影响选举的因素

![image-20220110160057336](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220110160057336.png)

涉及集合结构时首先考虑的

![image-20220111105402068](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220111105402068.png)

3.2 版本后支持mysql类似的left outer join $lookup

![image-20220111105705966](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220111105705966.png)

![image-20220111110101034](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220111110101034.png)

MongoDB 对使用引用的集合之间并无主外键检查
MongoDB 使用聚合框架的$lookup 来模仿关联查询
$lookup 只支持left outer join
$lookup 的关联目标（from）不能是分片表

##### 分桶模式

时序数据
物联网
智慧城市
智慧交通

利用文档内嵌数组，将一个时
间段的数据聚合到一个文档里。
大量减少文档数量
大量减少索引占用空间

##### 列转行设计模式

json数组中的数据也可以使用索引

![image-20220111135138820](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220111135138820.png)

![image-20220111135410167](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220111135410167.png)

##### 预聚合

统计使用

## 事务

### writeConcern

writeConcern 决定一个写操作落到多少个节点上才算成功。writeConcern 的取值包括：
• 0：发起写操作，不关心是否成功；
• 1~集群最大数据节点数：写操作需要被复制到指定节点数才算成功；
• majority：写操作需要被复制到大多数节点上才算成功。 推荐

发起写操作的程序将阻塞到写操作到达指定的节点数为止

![image-20220111155808334](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220111155808334.png)

### readPreference

决定使用从哪个节点来满足读请求--数据可用性

### redConcern

类似于Mysql的隔离级别

• available：读取所有可用的数据;
• **local**：读取所有可用且属于当前分片的数据; -- 保证了数据了准确性 **默认**
• **majority**：读取在大多数节点上提交完成的数据; 可以避免脏读   readcommit 
• linearizable：可线性化读取文档;
• snapshot：读取最近快照中的数据;  --可重复读

在复制集中local 和available 是没有区别的。两者的区别主要体现在分片集上。

readConcern: majority 的实现方式 MVCC

![image-20220111174021802](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220111174021802.png)





#### ChangeStream

Change Stream 是MongoDB 用于实现变更追踪的解决方案，类似于关系数据库的触
发器，但原理不完全相同

ChangStream 只推送readConcern: "majority" 的变更操作 

##### ChangeStream  使用场景

• 跨集群的变更复制——在源集群中订阅Change Stream，一旦得到任何变更立即写
入目标集群。
• 微服务联动——当一个微服务变更数据库时，其他微服务得到通知并做出相应的变
更。
• 其他任何需要系统联动的场景。

##### 注意事项

• Change Stream 依赖于oplog，因此中断时间不可超过oplog 回收的最大时间窗；
• 在执行update 操作时，如果只更新了部分数据，那么Change Stream 通知的也
是增量部分；
• 同理，删除数据时通知的仅是删除数据的_id。



#### 游标使用

如果一个游标已经遍历完，则会自动关闭；如果没有遍历完，则需要手动调用close()
方法，否则该游标将在服务器上存在10 分钟（默认值）后超时释放，造成不必要的资
源浪费。
但是，如果不能遍历完一个游标，通常意味着查询条件太宽泛，更应该考虑的问题是
如何将条件收紧。

#### 关于查询及索引

● 每一个查询都必须要有对应的索引
● 尽量使用覆盖索引Covered Indexes （可以避免读数据文件）
● 使用**projection** 来减少返回到客户端的的文档的内容

#### 术语

Config 配置节点    -- 通过配置节点找到数据所在的片

mongos 路由节点

mongod 数据节点

![image-20220116102005548](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220116102005548.png)

• 各种概念由小到大：
• 片键shard key：文档中的一个字段
• 文档doc ：包含shard key 的一行数据
• 块Chunk ：包含n 个文档
• 分片Shard：包含n 个chunk
• 集群Cluster： 包含n 个分片


![image-20220116103516599](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220116103516599.png)



## MongoDB索引

也是遵循最左前缀原则

MongoDB索引使用的是B-树--基于B数，但是子节点的数量可以超过两个，B树底端存储的索引可以快速找到数据的物理数据

![image-20220116111353937](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220116111353937.png)



#### 索引执行计划

![image-20220116111533596](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220116111533596.png)

#### Explain() --执行计划打印

```pytho
-- 写入10000条文档
for (var i=1;i<100000; i++)
db.col.insert(
{name:i, age:i,
date:new Date() } )
-- 查询
db.col.find({name:1111}).explain(true)  --- explain（true） 来打印执行计划
```



```json
{ "executionSuccess" : true,
"nReturned" : 1,		-- 返回了多少数据
"executionTimeMillis" : 58,		-- 执行时间
"totalKeysExamined" : 0,		-- 扫描了多少个索引项
"totalDocsExamined" : 99999,	--总共扫描了多少文档
"executionStages" : {
        "stage" : "COLLSCAN",  --stage 方式 全表扫描
        "filter" : {"name" : {"$eq" : 1111}},
        "nReturned" : 1,
        "executionTimeMillisEstimate" : 53,
        "works" : 100001,
        "advanced" : 1,
        "needTime" : 99999,
        "needYield" : 0,
        "saveState" : 783,
        "restoreState" : 783,
        "isEOF" : 1,
        "invalidates" : 0,
        "direction" : "forward",
        "docsExamined" : 99999
```

```json
完全命中索引
executionStats" : {
"executionSuccess" : true,
"nReturned" : 1,
"executionTimeMillis" : 3,
"totalKeysExamined" : 1,
"totalDocsExamined" : 1,
"executionStages" : {
        "stage" : "FETCH",
        "nReturned" : 1,
        "executionTimeMillisEstimate" : 0,
        "docsExamined" : 1,
        "alreadyHasObj" : 0,
        "inputStage" : {
            "stage" :
            "IXSCAN",
            "nReturned" : 1,
            "executionTimeMillisEstimate" : 0,
            "works" : 2,
            "advanced" : 1,…
}
```

#### MongoDB 索引类型

单键索引

##### 组合索引

组合索引的最佳方式：ESR原则
• 精确（Equal）匹配的字段放最前面
• 排序（Sort）条件放中间
• 范围（Range）匹配的字段放最后面
同样适用： ES, ER

多值索引
地理位置索引

##### 全文索引

3.+后才支持，数据量不大时使用

TTL索引

##### 部分索引

只针对部分数据做索引
![image-20220116113616446](C:\Users\luosh\AppData\Roaming\Typora\typora-user-images\image-20220116113616446.png)

只针对stage>5时做索引，或者只针对数据存在时做索引（数据不存在时索引无效），减小索引开销

哈希索引

#### 创建索引技巧

后台创建索引
db.member.createIndex( { city: 1}, {background: true} )
对BI / 报表专用节点单独创建索引
• 该从节点priority设为0
• 关闭该从节点，
• 以单机模式启动
• 添加索引（分析用）
• 关闭该从节点，以副本集模式启动

### Mongo

### MongoDB高可用如何实现

mongo状态：正常

 故障 -- 切换bak-mongo

​		故障可读    list 查询

​		故障不可读   读写都不行

 切换副 -- 数据过多， 新增加一套扩容

 迁移中 -- bakmongo -> 源mongo

多Mongo查询有什么限制？ 文件状态：上传中、删除中、底层超时

list接口，多游标查询，游标的关闭

自动切换如何实现



自动迁移如何实现

冷备：不做同步 一个集群切换到另一个集群

热备：

变更流复制

