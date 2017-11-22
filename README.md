<!--
Licensed to the Apache Software Foundation (ASF) under one or more
contributor license agreements.  See the NOTICE file distributed with
this work for additional information regarding copyright ownership.
The ASF licenses this file to You under the Apache License, Version 2.0
(the "License"); you may not use this file except in compliance with
the License.  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->

# [Apache PredictionIO (incubating)](http://predictionio.incubator.apache.org)

[![Build
Status](https://api.travis-ci.org/apache/incubator-predictionio.svg?branch=develop)](https://travis-ci.org/apache/incubator-predictionio)

Apache PredictionIO (incubating) is an open source machine learning framework
for developers, data scientists, and end users. It supports event collection,
deployment of algorithms, evaluation, querying predictive results via REST APIs.
It is based on scalable open source services like Hadoop, HBase (and other DBs),
Elasticsearch, Spark and implements what is called a Lambda Architecture.

To get started, check out http://predictionio.incubator.apache.org!


## Table of contents
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Bugs and Feature Requests](#bugs-and-feature-requests)
- [Documentation](#documentation)
- [Contributing](#contributing)
- [Community](#community)


## Installation

A few installation options available.

*   [Installing Apache PredictionIO (incubating) from Source
    Code](http://predictionio.incubator.apache.org/install/install-sourcecode/)
*   [Installing Apache PredictionIO (incubating) with
    Docker](http://predictionio.incubator.apache.org/community/projects/#docker-installation-for-predictionio)
    (community contributed)


## Quick Start

*   [Recommendation Engine Template Quick
    Start](http://predictionio.incubator.apache.org/templates/recommendation/quickstart/)
    Guide
*   [Similiar Product Engine Template Quick
    Start](http://predictionio.incubator.apache.org/templates/similarproduct/quickstart/)
    Guide
*   [Classification Engine Template Quick
    Start](http://predictionio.incubator.apache.org/templates/classification/quickstart/)
    Guide


## Bugs and Feature Requests

Use [Apache JIRA](https://issues.apache.org/jira/browse/PIO) to report bugs or request new features.

## Documentation

Documentation, included in this repo in the `docs/manual` directory, is built
with [Middleman](http://middlemanapp.com/) and publicly hosted at
[predictionio.incubator.apache.org](http://predictionio.incubator.apache.org/).

Interested in helping with our documentation? Read [Contributing
Documentation](http://predictionio.incubator.apache.org/community/contribute-documentation/).


## Community

Keep track of development and community news.

*   Subscribe to the user mailing list <mailto:user-subscribe@predictionio.incubator.apache.org>
    and the dev mailing list <mailto:dev-subscribe@predictionio.incubator.apache.org>
*   Follow [@predictionio](https://twitter.com/predictionio) on Twitter.


## Contributing

Read the [Contribute Code](http://predictionio.incubator.apache.org/community/contribute-code/) page.

You can also list your projects on the [Community Project
page](http://predictionio.incubator.apache.org//community/projects/).


## License

Apache PredictionIO (incubating) is under [Apache 2
license](http://www.apache.org/licenses/LICENSE-2.0.html).



### Show Example

一、数据存取；

1.启动服务: startAlls.sh
2.查看启动状态 :pio status 
3.创建应用:pio app new MyDemo
   记录：
      ID: 1
      Key: _dOppuRMc4v48AUViInKKK4rrDJPlchn7CR9eb6kI1qwHY1dNJManDZcIPKfOSN5
4.设置环境变量 ACCESS_KEY=_dOppuRMc4v48AUViInKKK4rrDJPlchn7CR9eb6kI1qwHY1dNJManDZcIPKfOSN5




5.上传数据：
curl -i -X POST http://localhost:7070/events.json?accessKey=$ACCESS_KEY \
-H "Content-Type: application/json" \
-d '{
  "event" : "rate",
  "entityType" : "user",
  "entityId" : "u0",
  "targetEntityType" : "item",
  "targetEntityId" : "i0",
  "properties" : {
    "rating" : 5
  }
  "eventTime" : "2014-11-02T09:39:45.618-08:00"
}'

6. 查询所有数据:
curl -i -X GET "http://localhost:7070/events.json?accessKey=$ACCESS_KEY"

7.批量导入数据：
 cd MyDemo
python data/import_eventserver.py --access_key $ACCESS_KEY

8.CRUD

 8.1.1查询某纪录 :curl -i -X GET http://localhost:7070/events/<your_eventId>.json?accessKey=<your_accessKey>

 支持查询条件:
   1. startTime
   2. untilTime
   3. entityType
   4. entityId
   5. event
   6. targetEntityType
   7. targetEntityId
   8. limit
   9. reversed
 8.1.2 查询时间范围纪录:curl -i -X GET "http://localhost:7070/events.json?accessKey=<your_accessKey>&startTime=<time in ISO8601 format>"
 

 8.2 删除某纪录
   curl -i -X DELETE http://localhost:7070/events/<your_eventId>.json?accessKey=<your_accessKey>
 8.3 获取某app所有数据:
   curl -i -X GET http://localhost:7070/events.json?accessKey=<your_accessKey>
 8.4 删除某app所有数据
    pio app data-delete <your_app_name>

二、模型训练：
     1.编译引擎；
        pio build --verbose

     2.训练模型:
       pio train
     3.部署引擎:
       pio deploy
       查看部署状态: http://localhost:8000
     4.使用引擎:
 curl -H "Content-Type: application/json" \
-d '{ "user": "1", "num": 4 }' http://localhost:8000/queries.json
     

三、数据分析及可视化
   1.(可选)导出数据:$PIO_HOME/bin/pio export --appid 1 --output /tmp/movies --format parquet
   2.启动zeppelin 服务：
    zeppelin/zeppelin-0.7.3-bin-netinst/bin/zeppelin-daemon.sh start
   3.http://localhost:8080
   4.Create new note
   5.Spark SQL 引擎创建临时表:
     sqlc.parquetFile("/tmp/movies").registerTempTable("events")
   6.执行分析语句(统计总数分布)
    %sql
SELECT entityType, event, targetEntityType, COUNT(*) AS c FROM events
GROUP BY entityType, event, targetEntityType
   
   7.%sql
SELECT event, COUNT(*) AS c FROM events GROUP BY event

   8.%sql
SELECT properties.rating AS r, COUNT(*) AS c FROM events
WHERE properties.rating IS NOT NULL GROUP BY properties.rating ORDER BY r


讨论重点

二: 图详解分解：
    目标： 图存边关系概要，hbase存储边详情；
    存储格式一：
    1.Janusgraph (GraphFrame 可以直接完成OLAP计算，对新人数据判断关系是否存在：1.存在不增加边，详情入库；2.关系不存在添加边，增加边同步JanusGraph,详情入库Hbase ) 存关联关系
    Hbase 存详情RowKEY 设计

   1.salt|OutVID|EVENT|InVID|(evnetTime)    properties…

   2.OutVID|EVENT|InVID|eventTime         properties…


   存储格式二：

   rowkey 随机生成 

  {
  "event" : "",
  "entityType" : "user",
  "entityId" : "u0",
  "targetEntityType" : "item",
  "targetEntityId" : "i0",
  "properties" : {
    “p1” : 5,
    “p2” : “abs”
    “p3”:[1,2,3,4]
  }
  "eventTime" : "2014-11-02T09:39:45.618-08:00"
