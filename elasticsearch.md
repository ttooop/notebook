# Elasticsearch
### 2019-7-19
Elasticsearch：分布式、可扩展、实时的搜索与数据分析引擎。不仅仅是全文搜索，还有结构化搜索、数据分析、复杂的人类语言处理、地理位置和对象间关联关系等。

集群内的原理、分布式文档存储、执行分布式检索和分片内部原理

## 基础入门
_Elasticsearch_ 是一个实时的分布式搜索分析引擎，它被用作全文检索、结构化搜索、分析以及这三个功能的组合。
Elasticsearch 不仅仅是 Lucene，并且也不仅仅只是一个全文搜索引擎。[](https://www.elastic.co/guide/cn/elasticsearch/guide/current/intro.html#id-1.4.3.5.1)  [](https://www.elastic.co/guide/cn/elasticsearch/guide/current/intro.html#id-1.4.3.5.2)它可以被下面这样准确的形容：

-   一个分布式的实时文档存储，_每个字段_  可以被索引与搜索
-   一个分布式实时分析搜索引擎
-   能胜任上百个服务节点的扩展，并支持 PB 级别的结构化或者非结构化数据

Elasticsearch 将所有的功能打包成一个单独的服务，这样你可以通过程序与它提供的简单的**RESTful API**进行通信， 可以使用自己喜欢的编程语言充当 web 客户端，甚至可以使用命令行（去充当这个客户端）。
### 和Elasticsearch交互
如果你正在使用[](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_talking_to_elasticsearch.html#id-1.4.3.14.3.2.1)  [](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_talking_to_elasticsearch.html#id-1.4.3.14.3.2.2)Java，在代码中你可以使用 Elasticsearch 内置的两个客户端：

节点客户端（Node client）

节点客户端[](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_talking_to_elasticsearch.html#id-1.4.3.14.3.3.1.2.1.1)作为一个非数据节点加入到本地集群中。换句话说，它本身不保存任何数据，但是它知道数据在集群中的哪个节点中，并且可以把请求转发到正确的节点。

传输客户端（Transport client）

轻量级的[](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_talking_to_elasticsearch.html#id-1.4.3.14.3.3.2.2.1.1)传输客户端可以将请求发送到远程集群。它本身不加入集群，但是它可以将请求转发到集群中的一个节点上。

两个 Java 客户端都是通过 _9300_ 端口并使用[](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_talking_to_elasticsearch.html#id-1.4.3.14.3.4.2) Elasticsearch 的原生 _传输_ 协议和集群交互。集群中的节点通过端口 9300 彼此通信。如果这个端口没有打开，节点将无法形成一个集群。

### RESTful API with JSON over HTTP
一个 Elasticsearch 请求和任何 HTTP 请求一样由若干相同的部件组成：[](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_talking_to_elasticsearch.html#id-1.4.3.14.4.4.1)[](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_talking_to_elasticsearch.html#id-1.4.3.14.4.4.2)
```
curl -X<VERB>  '<PROTOCOL>://<HOST>:<PORT>/<PATH>?<QUERY_STRING>'  -d '<BODY>'
```
被  `< >`  标记的部件：

`VERB`

适当的 HTTP  _方法_  或  _谓词_  :  ``GET`、 `POST`、 `PUT`、 `HEAD``  或者 `DELETE`。

`PROTOCOL`

`http`  或者  ``https`（如果你在 Elasticsearch 前面有一个 `https``  代理）

`HOST`

Elasticsearch 集群中任意节点的主机名，或者用  `localhost`  代表本地机器上的节点。

`PORT`

运行 Elasticsearch HTTP 服务的端口号，默认是  `9200`  。

`PATH`

API 的终端路径（例如  `_count`  将返回集群中文档数量）。Path 可能包含多个组件，例如：`_cluster/stats`  和  `_nodes/stats/jvm`  。

`QUERY_STRING`

任意可选的查询字符串参数 (例如  `?pretty`  将格式化地输出 JSON 返回值，使其更容易阅读)

`BODY`

一个 JSON 格式的请求体 (如果请求需要的话)

### 面向文档
Elasticsearch是面向文档的，意味着它存储整个对象或者文档，不仅存储文档，而且索引每个文档的内容，使之可以被检索。在Elasticsearch中，我们对文档进行索引、检索、排序和过滤，而不是对行列数据。
### JSON
Elasticsearch使用JavaScript Object Notation(JSON)作为文档的序列化格式。
eg:
```
{  "email":  "john@smith.com", 
 "first_name":  "John",  
 "last_name":  "Smith",  
 "info":  
 {  
 "bio":  "Eco-warrior and defender of the weak",  
 "age":  25,  
 "interests":  [  "dolphins",  "whales"  ] 
  },  
  "join_date":  "2014/05/01"  }
```
虽然原始的 `user` 对象很复杂，但这个对象的结构和含义在 JSON 版本中都得到了体现和保留。在 Elasticsearch 中将对象转化为 JSON 后构建索引要比在一个扁平的表结构中要简单的多。

### 索引员工文档
第一个业务需求是存储员工数据。[](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_indexing_employee_documents.html#id-1.4.3.17.2.1)  [](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_indexing_employee_documents.html#id-1.4.3.17.2.2)[](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_indexing_employee_documents.html#id-1.4.3.17.2.3)这将会以  _员工文档_  的形式存储：一个文档代表一个员工。存储数据到 Elasticsearch 的行为叫做  _索引_  ，但在索引一个文档之前，需要确定将文档存储在哪里。

一个 Elasticsearch 集群可以[](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_indexing_employee_documents.html#id-1.4.3.17.3.1)  [](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_indexing_employee_documents.html#id-1.4.3.17.3.2)[](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_indexing_employee_documents.html#id-1.4.3.17.3.3)包含多个  _索引_  ，相应的每个索引可以包含多个  _类型_  。[](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_indexing_employee_documents.html#id-1.4.3.17.3.6)  这些不同的类型存储着多个  _文档_  ，每个文档又有  [](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_indexing_employee_documents.html#id-1.4.3.17.3.8)多个  _属性_  。

### PUT员工信息
```
PUT /megacorp/employee/1  
{  
"first_name"  :  "John",  
"last_name"  :  "Smith",  
"age"  :  25, 
"about"  :  "I love to go rock climbing",  
"interests":  
[  "sports",  "music"  ]  
}
```
### GET检索员工信息
```
GET /megacorp/employee/1
```
将 HTTP 命令由 `PUT` 改为 `GET` 可以用来检索文档，同样的，可以使用 `DELETE` 命令来删除文档，以及使用 `HEAD` 指令来检查文档是否存在。如果想更新已存在的文档，只需再次 `PUT` 。
### 轻量搜索
请求搜索所有员工信息，使用_search
```
GET /megacorp/employee/_search
```
仍然使用索引库megacorp，以及类型employee，但与指定一个文档ID不同，这次使用的是_search，返回结果包括了三个文档，放在数组hits中。

条件搜索：
```
GET /megacorp/employee/_search?q=last_name:Smith
```
查询字符串（query-string）
仍在请求路径中使用_search端点，并将查询本身赋值给参数q=，返回结果给出了所有lastname是Smith的信息。
### 使用查询表达式搜索
Query-string搜索通过命令非常方便地进行即时性地即席搜索，但它有自身的局限性。elastic search提供一个丰富灵活的查询语言叫做**查询表达式**，它支持构建更加复杂健壮的查询。

领域特定语言（DSL），使用JSON构造了一个请求。
```
GET /megacorp/employee/_search
{
	"query":{
		"match":{
			"last_name":"Smith"
		}
	}
}
```
### 更复杂的搜索
```
GET /megacorp/employee/_search
{
	"query":{
		"bool":{
			"must":{
				"match":{
					"last_name":"smith"
				}
			},
			"filter":{
				"range":{
					"age":{"gt":30}
				}
			}
		}
	}
}
```
上述搜索过程是搜索姓氏为Smith且年龄大于30的员工，使用一个range过滤器，能找到年龄大于30的文档，其中gt表示大于（_great than）
### 全文搜索
```
GET /megacorp/employee/_search
{
    "query" : {
        "match" : {
            "about" : "rock climbing"
        }
    }
}
```
搜索喜欢rock climbing的员工，结果为：
```
{
   ...
   "hits": {
      "total":      2,
      "max_score":  0.16273327,
      "hits": [
         {
            ...
            "_score":         0.16273327, 
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
            "_score":         0.016878016,
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
其中的"_score"是相关性得分，Elasticsearch  默认按照相关性得分排序，即每个文档跟查询的匹配程度。第一个最高得分的结果很明显：John Smith 的  `about`  属性清楚地写着 “rock climbing” 。

但为什么 Jane Smith 也作为结果返回了呢？原因是她的  `about`  属性里提到了 “rock” 。因为只有 “rock” 而没有 “climbing” ，所以她的相关性得分低于 John 的。

这是一个很好的案例，阐明了 Elasticsearch 如何  _在_  全文属性上搜索并返回相关性最强的结果。Elasticsearch中的  _相关性_概念非常重要，也是完全区别于传统关系型数据库的一个概念，数据库中的一条记录要么匹配要么不匹配。
### 短语搜索
match的不是一个独立的单词，而是想要精确的匹配一系列单词或者短语，就要对match查询稍作调整，使用一个叫做match_phrase的查询：
```
GET /megacorp/employee/_search
{
    "query" : {
        "match_phrase" : {
            "about" : "rock climbing"
        }
    }
}
```
该查询返回的只有John Smith的文档，是精确匹配查询
### 高亮搜索
在搜索结果中*高亮*部分文本片段，以便让用户知道为何该文档符合查询条件，在elasticsearch中，在之前的查询中增加一个新的highlight参数：
```
GET /megacorp/employee/_search
{
    "query" : {
        "match_phrase" : {
            "about" : "rock climbing"
        }
    },
    "highlight": {
        "fields" : {
            "about" : {}
        }
    }
}
```
当执行该查询时，返回结果与之前一样，与此同时结果中还多了一个叫做 `highlight` 的部分。这个部分包含了 `about` 属性匹配的文本片段，并以 HTML 标签 `<em></em>` 封装.
```
{
   ...
   "hits": {
      "total":      1,
      "max_score":  0.23013961,
      "hits": [
         {
            ...
            "_score":         0.23013961,
            "_source": {
               "first_name":  "John",
               "last_name":   "Smith",
               "age":         25,
               "about":       "I love to go rock climbing",
               "interests": [ "sports", "music" ]
            },
            "highlight": {
               "about": [
                  "I love to go <em>rock</em> <em>climbing</em>"
               ]
            }
         }
      ]
   }
}
```
### 分析
elasticsearch有一个功能叫聚合(aggregation)，允许我们基于数据生成一些精细的分析结果。聚合与SQL中的GROUP BY 类似但更强大。
比如挖掘员工最受欢迎的兴趣爱好：
```
GET /megacorp/employee/_search
{
  "aggs": {
    "all_interests": {
      "terms": { "field": "interests" }
    }
  }
}
```
结果：
```
{
   ...
   "hits": { ... },
   "aggregations": {
      "all_interests": {
         "buckets": [
            {
               "key":       "music",
               "doc_count": 2
            },
            {
               "key":       "forestry",
               "doc_count": 1
            },
            {
               "key":       "sports",
               "doc_count": 1
            }
         ]
      }
   }
}
```
可以看到，两位员工对音乐感兴趣，一位对林业感兴趣，一位对运动感兴趣。这些聚合的结果数据并非预先统计，而是根据匹配当前查询的文档即时生成的。
如果想知道叫 Smith 的员工中最受欢迎的兴趣爱好，可以直接构造一个组合查询：
```
GET /megacorp/employee/_search
{
  "query": {
    "match": {
      "last_name": "smith"
    }
  },
  "aggs": {
    "all_interests": {
      "terms": {
        "field": "interests"
      }
    }
  }
}
```

聚合还支持分级汇总 。比如，查询特定兴趣爱好员工的平均年龄：
```
GET /megacorp/employee/_search
{
    "aggs" : {
        "all_interests" : {
            "terms" : { "field" : "interests" },
            "aggs" : {
                "avg_age" : {
                    "avg" : { "field" : "age" }
                }
            }
        }
    }
}
```
### 空集群
一个运行中的elasticsearch实例称为一个节点，而集群是由一个或者多个拥有相同cluster.name配置的节点组成，它们共同承担数据和负载的压力。当有节点加入集群中或者从集群中移除节点时，集群将会重新平均分布所有的数据。
当一个节点被选举成为  _主_  节点时， 它将负责管理集群范围内的所有变更，例如增加、删除索引，或者增加、删除节点等。 而主节点并不需要涉及到文档级别的变更和搜索等操作，所以当集群只拥有一个主节点的情况下，即使流量的增加它也不会成为瓶颈。 任何节点都可以成为主节点。我们的示例集群就只有一个节点，所以它同时也成为了主节点。

作为用户，我们可以将请求发送到  _集群中的任何节点_  ，包括主节点。 每个节点都知道任意文档所处的位置，并且能够将我们的请求直接转发到存储我们所需文档的节点。 无论我们将请求发送到哪个节点，它都能负责从各个包含我们所需文档的节点收集回数据，并将最终结果返回給客户端。 Elasticsearch 对这一切的管理都是透明的。
### 集群健康
Elasticsearch 的集群监控信息中包含了许多的统计数据，其中最为重要的一项就是  _集群健康_  ， 它在  `status`  字段中展示为  `green`  、  `yellow`或者  `red`  。
```
GET /_cluster/health
```
在一个不包含任何索引的空集群中，它将会有一个类似于如下所示的返回内容：
```
{
   "cluster_name":          "elasticsearch",
   "status":                "green", 
   "timed_out":             false,
   "number_of_nodes":       1,
   "number_of_data_nodes":  1,
   "active_primary_shards": 0,
   "active_shards":         0,
   "relocating_shards":     0,
   "initializing_shards":   0,
   "unassigned_shards":     0
}
```

`status`  字段指示着当前集群在总体上是否工作正常。它的三种颜色含义如下：

`green`所有的主分片和副本分片都正常运行。

`yellow`所有的主分片都正常运行，但不是所有的副本分片都正常运行。

`red`有主分片没能正常运行。

### 添加索引
索引——保存相关数据的地方，索引实际上是指向一个或者多个物理分片的逻辑命名空间。
一个分片是一个底层的工作单元，它仅保存了全部数据中的一部分。文档被存储和索引到分片内，但是应用程序是直接与索引而不是分片进行交互。
elasticsearch是利用分片将数据分发到集群内各处的，分片是数据的容器，文档保存在分片内，分片又被分配到集群内的各个节点里。当你的集群规模扩大或者缩小是，elasticsearch会自动地在各节点中迁移分片，使得数据仍然均匀分布在集群里。
一个分片可以是主分片或者副本分片。索引内任意一个文档都归属于一个主分片，所以主分片地数目决定着索引能够保存的最大数据量。
一个副本分片只是一个主分片的拷贝。副本分片作为硬件故障时保护数据不丢失的冗余备份，并为搜索和返回文档等读操作提供服务。
在索引建立的时候就已经确定了主分片数，但是副本分片数可以随时修改。
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE2NTI4OTA0MV19
-->