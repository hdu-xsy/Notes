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

## 结构化搜索bitset机制和caching机制
* 再倒排索引中查找搜索串，获取document list
* 为每个在倒排索引中搜索到的结果，构建一个bitset，就是一个二进制的数组，用来标识
* 遍历每个过滤条件对应的bitset，优先从最稀疏的开始搜索，查找满足所有条件的document
* caching bitset，跟踪query，在最近256个query中超过一定次数的过滤次数，次数不固定，就会自动缓存这个filter对应的bitset，filter比query好在会caching
* filter大部分情况在query之前执行过滤尽可能多数据，query会计算doc对搜索条件的relevance score，还会根据score排序，filter都不
* document更改时 cached bitset自动更新
* 以后只要有相同filter条件的，会直接来使用这个过滤条件对应的cached bitset

#索引
##查看集群中索引
GET /_cat/indices?v

##mapping
GET /my_index/_mapping/my_type
##增加索引
PUT /es_index?pretty

##删除索引
DELETE /test_index?pretty

##创建索引
#############################################################
#Indices created in Elasticsearch 6.0.0 or later may only contain a single mapping type. Indices created in 5.x with multiple mapping types will continue to function as before in Elasticsearch 6.x. Mapping types will be completely removed in Elasticsearch 7.0.0.
#############################################################
PUT /my_index_v1
{
  "settings": {
      "number_of_shards" :   5,
      "number_of_replicas" : 1,
      "analysis": {
          "analyzer": {
              "es_std": {
                  "type":"standard"
              }
          }
      }
  },
  "mappings": {
    "my_type": {
      "properties": {
        "my_field":{
          "type" : "keyword"
        },
        "timestamp": {
            "type": "date",
            "format": "strict_date_optional_time"
        },
        "text": {
            "type": "text"
        },
         "first_name": {
              "type":     "text",
              "copy_to":  "full_name" 
          },
          "last_name": {
              "type":     "text",
              "copy_to":  "full_name" 
          },
          "full_name": {
              "type":     "text"
          }
      }
    }
  }
}

##索引别名
PUT /my_index_v1/_alias/my_index

##查看别名
GET /*/_alias/my_index
GET /my_index_v1/_alias/*

##零停机迁移索引
POST /_aliases
{
    "actions": [
        { "remove": { "index": "my_index_v1", "alias": "my_index" }},
        { "add":    { "index": "my_index_v2", "alias": "my_index" }}
    ]
}

#数据输入输出
##增
PUT /es_index/es_type/es_id
{
  "username":"admin",
  "password":"admin"
}

##查
GET /es_index/es_type/es_id

##改
POST /es_index/es_type/es_id/_update
{
  "doc":{
    "password":"P@5$W0RD",
    "id":1
  }
}

##删
DELETE /es_index/es_type/es_id

#测试用例数据
PUT /hdu?pretty
PUT /hdu/se/SJJG
{
  "s_number":44,
  "t_name":"XiaoWang",
  "class":"6-111",
  "time":["1-34","4-345","5-89"]
}
GET /hdu/se/GJJG
PUT /hdu/se/JSJZCYL
{
  "s_number":33,
  "t_name":"XiaoWang",
  "class":"11-121",
  "time":["2-89","4-12"]
}
PUT /hdu/se/SJKYL
{
  "s_number":22,
  "t_name":"XiaoWang",
  "class":"12-111",
  "time":["1-67"]
}
PUT /hdu/se/JavaEE
{
  "s_number":55,
  "t_name":"XiaoChen",
  "class":"6-111",
  "time":["1-67","3-345","4-89"]
}

#Query String Search
##搜索全部
GET /hdu/se/_search

## 搜索xx包含且按yy降序排序
GET /hdu/se/_search?q=t_name:XiaoWang&sort=s_number:desc

#QueryDSL
## 查询所有
GET /hdu/se/_search 
{
  "query": {
    "match_all": {}
  }
}

## 查询xx包含a且按yy降序排序
GET /hdu/se/_search 
{
  "query": {
    "match": {
      "t_name":"XiaoWang"
    }
  },
  "sort":
    {"s_number":"desc"}
}

## 分页查询 从a个商品开始查 查b个
GET /hdu/se/_search 
{
  "query": {
    "match_all": {}
  },
  "from": 0,
  "size": 1
}

## 投影运算
GET /hdu/se/_search 
{
  "query": {
    "match_all": {}
  },
  "_source":["s_number","t_name"]
}

#Query Filter
## FIELE包含TEXT 且 FIELE 大于n
GET /hdu/se/_search 
{
  "query": {
    "bool": {
      "must": {
          "match": {
            "t_name":"XiaoWang"
          }
      },
      "filter": { 
        "range":{
          "s_number":{"gt",33}
        }
      }
    }
  }
}

#Full-Text Search 全文检索
GET /hdu/se/_search 
{
  "query": {
    "match": {
      "time":"34 89"
    }
  }
}

#Phrase Search 短语搜索
GET /hdu/se/_search 
{
  "query": {
    "match_phrase": {
      "time": "34"
    }
  }
}

#Highlight Search 高亮搜索
GET /hdu/se/_search 
{
  "query": {
    "match_all": {}
  },
  "highlight": {
    "fields": {
      "t_name": {}
    }
  }
}

#聚合
##文本field的fielddata改为true
GET /hdu/_mapping/se 
{
  "properties":{
    "t_name":{
      "type":"text",
      "fielddata":true
    }
  }
}

##计算field下的所有Value出现次数
GET /hdu/se/_search 
{
  "size":0,
  "aggs": {
    "name": {
      "terms": {
        "field": "s_number"
      }
    }
  }
}

## 计算field为TEXT的每个field的Value出现次数
GET /hdu/se/_search
{
  "size": 0,
  "query": {
    "match": {
      "t_name": "XiaoWang"
    }
  },
  "aggs": {
    "NAME": {
      "terms": {"field": "s_number"}
    }
  }
}

## 按FIELD1分组计算FIELD2的平均值,并排序
GET hdu/se/_search
{
  "size": 0, 
  "aggs":{
    "name":{
      "terms": {
        "field": "t_name","order": {
          "_term": "desc"
        }
      },
      "aggs":{
        "name":{
          "avg": {
            "field": "s_number"
          }
        }
      }
    }
  }
}

## 先取范围再分组再计算平均值
GET /hdu/se/_search
{
  "size": 0,
  "aggs": {
    "NAME": {
      "range": {
        "field":"s_number",
        "ranges": [
          {"from": 0,"to": 100}
        ]
      },
      "aggs": {
        "NAME": {
          "terms": {
            "field": "t_name"
          },
          "aggs":{
            "name":{
              "avg": {
                "field": "s_number"
              }
              }
            }
          }
        }
      }
    }
  }
}

#结构化搜索Term Filter
## 根据Field的Value搜索
GET /hdu/se/_search
{
  "query": {
    "constant_score": {
      "filter": {
        "term": {
          "time": "89"
        }
      }
    }
  }
}

## 查看分词
GET /hdu/_analyze
{
  "field": "class", 
  "text": "1-111"
}

## not_analyzer 不分词
PUT /hdu
{
  "mappings": {
    "se": {
      "properties": {
        "class":{
            "type" : "keyword"
        }
      }
    }
  }
}

# 结构化搜索bool组合多个filter条件来搜索数据
## 搜索xx为aa或者yy为bb同时zz不为cc的type
GET /hdu/se/_search 
{
  "query":{
    "constant_score":{
      "filter": {
        "bool": {
          "should":[
            {"term":{"t_name":"XiaoWang"}},
            {"term":{"class":"6-111"}}
          ],
          "must_not":{
            "term":{"class":"7-111"
            }
          }
        }
      }
    }
  }
}
# 结构化搜索 范围
GET /hdu/se/_search
{
    "query" : {
        "constant_score" : {
            "filter" : {
                "range" : {
                    "s_number" : {
                        "gte" : 20,
                        "lt"  : 40
                    }
                }
            }
        }
    }
}

# 结构化搜索 缺失搜索
GET /hdu/se/_search
{
    "query" : {
        "constant_score" : {
            "filter": {
                "missing" : { "field" : "class" }
            }
        }
    }
}

#全文搜索 提高精度
GET /hdu/se/_search
{
    "query": {
        "match": {
            "time": {      
                "query":"4 345",
                "operator": "and"
            }
        }
    }
}

#全文搜索 控制精度
GET /hdu/se/_search
{
  "query": {
    "match": {
      "time": {
        "query":"1 4 67 89",
        "minimum_should_match": "75%"
      }
    }
  }
}

# 全文搜索bool组合查询 控制精度
GET /hdu/se/_search
{
  "query": {
    "bool": {
      "must":     { "match": { "class": "6-111" }},
      "must_not": { "match": { "t_name": "XiaoChen"  }},
      "should": [
                  { "match": { "time":"1" }},
                  { "match": { "time":"4" }},
                  { "match": { "time":"67"}},
                  { "match": { "time":"89"}}
      ],
      "minimum_should_match": 2
    }
  }
}

# 全文搜索 查询语句提升权重
GET /_search
{
    "query": {
        "bool": {
            "must": {
                "match": {  
                    "time": {
                        "query":"1 4",
                        "operator": "and"
                    }
                }
            },
            "should": [
                { "match": {
                    "class": {
                        "query": "6-111",
                        "boost": 3 
                    }
                }},
                { "match": {
                    "class": {
                        "query": "12-111",
                        "boost": 2 
                    }
                }}
            ]
        }
    }
}

# 最大化查询（Disjunction Max Query）
GET /hdu/se/_search
{
    "query": {
        "dis_max": {
            "queries": [
                { "match": { "time": "1 34" }},
                { "match": { "class":"6-111" }}
            ]
        }
    }
}

# Multi_Match （）
GET /hdu/se/_search
{
  "query":{
    "multi_match": {
        "query":                "34",
        "type":                 "best_fields", 
        "fields":               [ "class", "time" ],
        "tie_breaker":           0.3,
        "minimum_should_match": "30%" 
    }
  }
}

#近似匹配
##短语匹配 （相邻）
GET /hdu/se/_search
{
  "query": {
    "match_phrase": {
      "time": "4 89"
    }
  }
}

##slop 参数 （相隔多远时仍然能将文档视为匹配）
GET /hdu/se/_search
{
    "query": {
        "match_phrase": {
            "time": {
                "query": "4 89",
                "slop":  1
            }
        }
    }
}

## 

