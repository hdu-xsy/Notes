## ES
* 索引(Index)  
ES将数据存储于一个或多个索引中，索引是具有类似特性的文档的集合。类比传统的关系型数据库领域来说，索引相当于SQL中的一个数据库，或者一个数据存储方案(schema)。索引由其名称(必须为全小写字符)进行标识，并通过引用此名称完成文档的创建、搜索、更新及删除操作。一个ES集群中可以按需创建任意数目的索引。
* 类型(Type)  
类型是索引内部的逻辑分区(category/partition)，然而其意义完全取决于用户需求。因此，一个索引内部可定义一个或多个类型(type)。一般来说，类型就是为那些拥有相同的域的文档做的预定义。例如，在索引中，可以定义一个用于存储用户数据的类型，一个存储日志数据的类型，以及一个存储评论数据的类型。类比传统的关系型数据库领域来说，类型相当于“表”。
* 文档(Document)  
文档是Lucene索引和搜索的原子单位，它是包含了一个或多个域的容器，基于JSON格式进行表示。文档由一个或多个域组成，每个域拥有一个名字及一个或多个值，有多个值的域通常称为“多值域”。每个文档可以存储不同的域集，但同一类型下的文档至应该有某种程度上的相似之处。
* 映射(Mapping)  
ES中，所有的文档在存储之前都要首先进行分析。用户可根据需要定义如何将文本分割成token、哪些token应该被过滤掉，以及哪些文本需要进行额外处理等等。另外，ES还提供了额外功能，例如将域中的内容按需排序。事实上，ES也能自动根据其值确定域的类型。
* 节点(Node)  
运行了单个实例的ES主机称为节点，它是集群的一个成员，可以存储数据、参与集群索引及搜索操作。类似于集群，节点靠其名称进行标识，默认为启动时自动生成的随机Marvel字符名称。用户可以按需要自定义任何希望使用的名称，但出于管理的目的，此名称应该尽可能有较好的识别性。节点通过为其配置的ES集群名称确定其所要加入的集群。
* 分片(Shard)和副本(Replica)  
ES的“分片(shard)”机制可将一个索引内部的数据分布地存储于多个节点，它通过将一个索引切分为多个底层物理的Lucene索引完成索引数据的分割存储功能，这每一个物理的Lucene索引称为一个分片(shard)。每个分片其内部都是一个全功能且独立的索引，因此可由集群中的任何主机存储。创建索引时，用户可指定其分片的数量，默认数量为5个。 Shard有两种类型：primary和replica，即主shard及副本shard。Primary shard用于文档存储，每个新的索引会自动创建5个Primary shard，当然此数量可在索引创建之前通过配置自行定义，不过，一旦创建完成，其Primary shard的数量将不可更改。Replica shard是Primary Shard的副本，用于冗余数据及提高搜索性能。每个Primary shard默认配置了一个Replica shard，但也可以配置多个，且其数量可动态更改。ES会根据需要自动增加或减少这些Replica shard的数量。ES集群可由多个节点组成，各Shard分布式地存储于这些节点上。ES可自动在节点间按需要移动shard，例如增加节点或节点故障时。简而言之，分片实现了集群的分布式存储，而副本实现了其分布式处理及冗余功能。
* 面向文档

## 基本操作
* 检测集群的健康状态

```
GET /_cat/health?v
green：每个索引的primary shard和replica shard都是active状态
yellow：部分replica shard不是active
red：部分primary shard不是active
```

* 快速查看集群中有哪些索引

```
GET /_cat/indices?v
```

* mapping


```
GET  /index/_mapping/type
```

* 增加索引
  

```
PUT /indexname?pretty
```

* 删除

```
DELETE /indexname?pretty
```

* 增

```
PUT /index/type/id{
  "key"："value"
}
```

* 查

```
GET /index/type/id
```

* 改

```
PUT /index/type/id{json} 必须包含全部field(覆盖)
更新部分field：
POST /index/type/id/_update {
  "doc":{json}
}
```

* 删

```
DELETE /index/type/id
```

## 搜索方式
### query string search
* 搜索全部  

```
GET /index/type/_search
took：耗费毫秒数
timed_out：是否超时
_shards：数据拆成了五个分片，所以对于搜索请求，会打到所有的primary shard（或者是他的某个replica shard）
hits.total：查询结果的数量
hits.max_score：匹配分数，值越大匹配越高
hits.hits：详细数据
```

* 搜索xx包含a且按yy降序排序

```
GET /index/type/_search?q=xx:a&sort=yy:desc

```

### query DSL

```
GET /index/type/_search {
   "query":{query}
}
```

* 查询所有

```
"query":{"match_all":{}}
```

* 查询xx包含a且按yy降序

```
“query": {
    "match": {
        "xx"="a"
    }
},
"sort": [
    {"yy":"desc"}
]
```

* 分页查询，从a个商品开始查，查b个

```
”query":{"match_all":{}},
"from":a,(从零开始计数)
"size":b
```

* 投影运算

```
"query":{"match_all":{}},
"_source":["xx","yy"]
```

### query filter
* key包含value且key大于n

```
GET /index/type/_search {
   "query":{
     "bool":{
       "must":{
         "match":{
           "key":"value"
         }
       },
       "filter":{
         "range":{
           "key":{"gt",n}
         }
       }
     }
   }
}
```

### full-text search 全文检索
```
GET /index/type/_search {
  "query":{
    "match":{
      "key":"value"
    }
  }
}
```

### phrase search 短语搜索
```
GET /index/type/_search {
  "query":{
    "match_phrase":{
      "key":"value"
    }
  }
}
```
### highlight search高亮搜索

```
GET /index/type/_search {
  "query":{
    "match":{
      "key":"value"
    }
  },
  "highlight":{
    "fields":{
      "key":{}
    }
  }
}
```

## 聚合
* 计算每个field下的value出现数量

```
GET /index/type/_search {
  "aggs":{
    "name":{
      "terms":{"field":"field"}
    }
  }
}
```

* 文本field的fielddata改为true

```
GET /index/_mapping/type {
  "properties":{
    "field":{
      "type":"text",
      "fielddata":true
    }
  }
}
```

* 计算key为value的每个field下的value出现数量

```
GET /index/type/_search {
  "size":0,
  "query":{
    "match":{
      "key":"value"
    }
  },
  "aggs":{
    "name":{
      "terms":{"field":"field"}
    }
  }
}
```

* 按field的value分组计算key平均值  

```
GET /index/type/_search {
  "aggs":{
    "name":{
      "terms":{"field":"field"},
      "aggs": {
        "name":{
          "avg":{"field":"field"}
        }
      }
    }
  }
}
```  

* 按field的value分组计算key平均值降序

```
GET /index/type/_search {
  "size":0,
  "aggs":{
    "name":{
      "terms":{"field":"field","order":{"name2":"desc"}},
      "aggs": {
        "name2":{
          "avg":{"field":"field"}
        }
      }
    }
  }
}
```

* 先按key的value分组后再按field的value分组计算key平均值

```
GET /index/type/_search {
  "size":0,
  "aggs":{
    "name":{
      "range":{
        "key":"value",
        "ranges":[
          {"from":n,"to":m},……
        ]
      },
      "aggs": {
        "name":{
          "terms":{"field":"field"},
          "aggs":{
            "name":{
              "avg":{"field":"field"}
            }
          }
        }
      }
    }
  }
}
```

## 结构化搜索term filter

### 根据field的value搜索

```
GET /index/type/_search {
  "query":{
    "constant_score":{
      "filter":{
      "term": {“field":value}
      }
    }
  }
}
```
### 查看分词

```
GET /index/_analyzer
{
  "field":"field",
  "text":value
}
```

### not_analyzer 不分词

```
PUT /index
{
  "mappings":{
    "type":{
      "properties": {
        "field":{
          "type":"string",
          "index":"not_analyzer"
        }
      }
    }
  }
}
```

## 结构化搜索bitset机制和caching机制
* 再倒排索引中查找搜索串，获取document list
* 为每个在倒排索引中搜索到的结果，构建一个bitset，就是一个二进制的数组，用来标识
* 遍历每个过滤条件对应的bitset，优先从最稀疏的开始搜索，查找满足所有条件的document
* caching bitset，跟踪query，在最近256个query中超过一定次数的过滤次数，次数不固定，就会自动缓存这个filter对应的bitset，filter比query好在会caching
* filter大部分情况在query之前执行过滤尽可能多数据，query会计算doc对搜索条件的relevance score，还会根据score排序，filter都不
* document更改时 cached bitset自动更新
* 以后只要有相同filter条件的，会直接来使用这个过滤条件对应的cached bitset


## 结构化搜索bool组合多个filter条件来搜索数据
