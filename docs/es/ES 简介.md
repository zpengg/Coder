## ELK
Elastic Stack(ELk) 相关产品
 - **Elasticsearch** 一个基于JSON的分布式搜索和分析引擎  
 - **Kibana** 可视化、一个可扩展的用户界面  
 - Beats 采集数据工具  
 - Logstash 第三方数据库中提取数据 

## 概念
- 集群  
- 节点  
- 分片shard  
- 副本replica 
- 索引index   
- ~~类型type~~    
- 文档doc  

类比  
ES：indices --> ~~types~~ --> documents --> fields  
DB：databases --> tables --> rows --> columns  

### 分片shard
#### 作用
分片是读写分离，以及减少单点负载，这样把数据的读写分摊到不同的机器

#### 配置
需要配置一个主分片数量，并永不再改变
(后提供了_split接口可改变，read-only条件下）

默认设置5个主分片  
ES 的每个分片（shard）都是lucene的一个index，而lucene的一个index只能存储20亿个文档，所以一个分片也只能最多存储20亿个文档。  

#### 分片选择公式
shard = hash(routing) % number_of_primary_shards  
routing 默认为文档id  

文档会存到其中一个shard，并复制到对应的replica
![](http://zpengg.oss-cn-shenzhen.aliyuncs.com/img/230539dde644b8ccaacd96cb56e6d852.png)


**分片数过多**会导致：  
1、  会导致打开比较多的文件 句柄有限
2、  分片是存储在不同机器上的，分片数越多，机器之间的交互也就越多；  

**分片数太少**导致：  
单个分片索引过大，降低整体的检索速率  

将单个分片存储存储索引数据的大小控制在20G左右；绝对不要超过50G ， 否则性能很差
最终分片数量约 = 数据总量/20G  

### 副本replica
主分片不可用时，集群选择副本提升为主分片。  
ES禁止同一分片的主分片,副本在同一个节点上。   
比如两个节点其中
node1 有某个索引的分片1、2、3和副本分片4、5，
node2 有该索引的分片4、5和副本分片1、2、3。  

|node1|node2|
|-|-|
|p1|r1|
|p2|r2|
|p3|r3|
|r4|p4|
|r5|p5|

文档index过程，shard同步修改到其他节点的replica后，才返回结果到客户端。  

### 索引

[数据结构]( http://blog.csdn.net/whichard/article/details/90753727?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2)

倒排索引

![](https://img2018.cnblogs.com/blog/874963/201901/874963-20190127184959667-1135956344.png)

[[FST]]
skipList



### 文档准实时写入过程
> Elasticsearch provides real-time search and analytics for all types of data

2.x准实时 到 7.x实时
near real-time -> real-time

在 Elasticsearch 中，写入和打开一个新段的轻量的过程叫做 refresh 。 默认情况下每个分片会每秒自动刷新一次。这就是为什么我们说 Elasticsearch 是 近 实时搜索: 文档的变化并不是立即对搜索可见，但会在一秒之内变为可见。

Lucene 允许新段被写入和打开—​使其包含的文档在未进行一次完整提交时便对搜索可见。 这种方式比进行一次提交代价要小得多，并且在不影响性能的前提下可以被频繁地执行。

新段写入到 灰色为文件系统缓存

Elasticsearch 增加了一个 translog ，或者叫事务日志，在每一次对 Elasticsearch 进行操作时均进行了日志记录。通

默认 translog 是每 5 秒被 fsync 刷新到硬盘， 或者在每次写请求完成之后执行

基本上，这意味着在整个请求被 fsync 到主分片和复制分片的translog之前，你的客户端不会得到一个 200 OK 响应。
![](http://zpengg.oss-cn-shenzhen.aliyuncs.com/img/7ff5c9c4628d3971a5530a482d0e895e.png)

refresh

![](http://zpengg.oss-cn-shenzhen.aliyuncs.com/img/d76e144a038746e6e908c1a8d60d309f.png)

flush

![](https://www.elastic.co/guide/cn/elasticsearch/guide/current/images/elas_1109.png)

## 搭建ES 

原本已经装好/app/ebn，浏览器10.78.101.3:9200 访问后可以看到   
![](http://zpengg.oss-cn-shenzhen.aliyuncs.com/ca2accf57de7ad4b7f99a97e8c857790.png)  

搭建一个新的 需要修改一下配置

![](http://zpengg.oss-cn-shenzhen.aliyuncs.com/img/b3d206a836eb83c765d9e310bcb18a20.png)

## 基本操作
新版本下主要通过HTTP访问

### CRUD
通过HTTP方法GET来检索文档，同样的，我们可以使用DELETE方法删除文档，使用HEAD方法检查某文档是否存在。如果想更新已存在的文档，我们只需再PUT一次。  

```
POST /index/type/xxx 创建
DELETE /index/type/xxx 删除
PUT /index/type/xxx 更新或创建
GET /index/type/xxx 查看
```

### 搜索
DSL 语句查询

term：查询某个字段里含有某个关键词的文档, 只能查单个词 
terms：查询某个字段里含有多个关键词的文档, 多个词 或关系 
match: 知道分词器的存在，会对field进行分词操作，然后再查询 
match_phrase: 短语搜索，要求所有的分词同时出现在文档中，位置紧邻。 
... 
可以查看[更多查询](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/5.6/java-rest-high-query-builders.html)  
```
GET /megacorp/employee/_search
{
    "query" : {
        "match" : {
            "last_name" : "Smith"
        }
    }
}
```
```
GET /megacorp/employee/_search
{
    "query" : {
        "filtered" : {
            "filter" : {
                "range" : {
                    "age" : { "gt" : 30 } <1>
                }
            },
            "query" : {
                "match" : {
                    "last_name" : "smith" <2>
                }
            }
        }
    }
}
```
会同时返回 含相关字段的结果 和 相关性评分   
```
GET /megacorp/employee/_search
{
    "query" : {
        "match" : {
            "about" : "rock climbing"
        }
    }
}

{
   ...
   "hits": {
      "total":      2,
      "max_score":  0.16273327,
      "hits": [
         {
            ...
            "_score":         0.16273327, <1>
            "_source": {
               "first_name":  "John",
               "last_name":   "Smith",
               "age":         25,
               "about":       "I love to go rock climbing",
               "interests": [ "sports", "music" ]
            }
         },
         {
            ...
            "_score":         0.016878016, <2>
            "_source": {
               "first_name":  "Jane",
               "last_name":   "Smith",
               "age":         32,
               "about":       "I like to collect rock albums",
               "interests": [ "music" ]
            }
         }
      ]
   }
}
```
### 聚合

分为以下几种：  
指标聚合 Metrics Aggregation: 需要计算指标的,如平均值、最大最小值  
桶聚合 Bucket Aggregation: 给定边界，数据落入不同分类的桶中  

管道聚合（v5.6是实验性） Pineline Aggregation：将其他聚合结果基础上进行聚合 比如 每月 sum（sell）之后求 max 
矩阵聚合（实验性）  

桶聚合的结果，每个桶代表一组文档，因此桶聚合可以嵌套对里面的数据进行聚合  
除了矩阵聚合，其他几种都可以加上脚本简单计算（相当于自定义指标）  

> 5.x后对排序，聚合这些操作用单独的数据结构(fielddata)缓存到内存里了，解释在此[fielddata](https://www.elastic.co/guide/en/elasticsearch/reference/current/fielddata.html)

> text类型的字段是要分词的, 默认不能作为field字段作为聚合用。Fielddata is disabled on text fields by default


## java RestHighLevelClient 请求示例
ES Java 客户端目前主要提供两种方式，分别是 ~~Java API~~ 和 Java REST Client：  

查询请求模版  
```
//构造查询请求
SearchRequest searchRequest = new SearchRequest(); 
SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder(); 
searchSourceBuilder.query(QueryBuilders.matchAllQuery()); 
searchRequest.source(searchSourceBuilder); 
//查询
SearchResponse resp = client.search(searchRequest);

// 插入等有类似IndexRequest 不再列举。
```

聚合请求  
![agg](http://zpengg.oss-cn-shenzhen.aliyuncs.com/613e47cdc9ae1215e9bcbdcf162ffd25.png)
请求结果  
![aggResult](http://zpengg.oss-cn-shenzhen.aliyuncs.com/c04d0cd995c0088b454e164c88ca9798.png)

神策
![](http://zpengg.oss-cn-shenzhen.aliyuncs.com/img/60e6c6e85624119a5c6f96c1c7e4fc56.png)


## ESSQL
目前我们项目使用的 sql 查询
SQL 特性是 xpack 的免费功能，语法为直接把sql语句从query传入
但jdbc 是付费功能 需要破解

以上提到的，是dsl语句查询。
另有兼容sql的查询方法，但目前与目前测试机器上的版本不匹配。
5.x需要安装相关[插件](https://www.jianshu.com/p/b4032a7e5497)

V6.3开始官方提供sql查询，但有取消的可能。
SQL 特性是 xpack 的免费功能，语法为直接把sql语句从query传入
```
POST /_xpack/sql?format=txt
{
    "query": "SELECT * FROM twitter"
}
```

v7.5 导入默认数据，请求url 有所变更  
```
POST _sql?format=txt
{
  "query":"SELECT COUNT(*), MONTH_OF_YEAR(order_date) AS month_of_year, AVG(products.min_price) AS avg_price FROM kibana_sample_data_ecommerce GROUP BY month_of_year"
}
```

### 如何处理join
这篇文章说得比较详细了
https://blog.csdn.net/laoyang360/article/details/88784748

单文档数据变更满足ACID
建议扁平化， 尽量将业务转化为没有关联关系的文档形式，在文档建模处多下功夫，以提升检索效率 索引/搜索过程是快速、无锁


目前解决办法： 

1.应用端关联
将第一次查询结果和第二次查询结果组合后，返回给用户。

2.宽表冗余存储
其他不推荐 Nested，Type

## 官方提供的kibana地址 有测试数据
https://demo.elastic.co/app/kibana#/dev_tools/console?_g=()
