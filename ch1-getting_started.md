# Getting started with Elasticsearch

准备好如何使用 Elasticsearch 来测试驱动使用 REST APIs 来存储、查询、分析数据了吗？
>Ready to take Elasticsearch for a test drive and see for yourself how you can use the REST APIs to store, search, and analyze data?

遵循如下开始教程：
>Step through this getting started tutorial to:

1. 获取一个 Elasticsearch 实例并启动运行
2. 添加索引实例文档
3. 使用 Elasticsearch 查询语法来查询文档
4. 使用桶（Bucket） 和 指标（metrics） 来聚合分析返回结果
>1. Get an Elasticsearch instance up and running
>2. Index some sample documents
>3. Search for documents using the Elasticsearch query language
>4. Analyze the results using bucket and metrics aggregations

需要更多内容？
>Need more context?

查看[Elasticsearch 介绍](https://www.elastic.co/guide/en/elasticsearch/reference/current/elasticsearch-intro.html)来学习和理解 Elasticsearch 基础的工作原理。如果你已经熟悉 Elasticsearch 且希望知道ELK 如何工作，请跳转[Elastic Stack 教程](https://www.elastic.co/guide/en/elastic-stack-get-started/7.2/get-started-elastic-stack.html)来了解如何使用 Elasticsearch, Kibana, Beats 和 Logstash 来设置系统监控的解决方案。
>Check out the Elasticsearch Introduction to learn the lingo and understand the basics of how Elasticsearch works. If you’re already familiar with Elasticsearch and want to see how it works with the rest of the stack, you might want to jump to the Elastic Stack Tutorial to see how to set up a system monitoring solution with Elasticsearch, Kibana, Beats, and Logstash.


## 安装（Installation）
todo...

## 探索集群（Exploring Your Cluster）
### 关于 REST API
>The REST API

现在我们有了可以启动并允许的节点（或集群），下一步是理解如何使用好 Elasticsearch。幸运的是Elasticsearch 提供了非常全面且有力的 REST API 来让我们交互，可以使用 API 完成如下的功能：
>Now that we have our node (and cluster) up and running, the next step is to understand how to communicate with it. Fortunately, Elasticsearch provides a very comprehensive and powerful REST API that you can use to interact with your cluster. Among the few things that can be done with the API are as follows:

1. 检查集群、节点和索引的健康状态和统计
2. 管理集群、节点和索引数据和元数据
3. 对索引完整的增删改查和查询
4. 运行高级的查询操作，如分页、排序、过滤、脚本、聚合和其他操作
>1. Check your cluster, node, and index health, status, and statistics
>2. Administer your cluster, node, and index data and metadata
>3. Perform CRUD (Create, Read, Update, and Delete) and search operations against your indexes
>4. Execute advanced search operations such as paging, sorting, filtering, scripting, aggregations, and many others

### 集群健康状态（Cluster Health）
todo...
### List All Indices
### Create an Index
### Index and Query a Document
### Delete an Index

## 修改数据（Modifying Your Data）
todo...
### Updating Documents
### Deleting Documents
### Batch Processing

## 探索数据（Exploring Your Data）
todo...
### The Search API
### Introducing the Query Language
### Executing Searches
### Executing Filters
### Executing Aggregations

## 总结（Conclusion）
Elasticsearch 是即简单又复杂的产品。我们到目前为止学习了关于 Elasticsearch 的基础，及如何查看和使用 REST APIs。希望这个教程能给你更好的理解 Elasticsearch 是什么，且更重要的是激发你未来去探索 Elastissearch 更丰富的功能。
>Elasticsearch is both a simple and complex product. We’ve so far learned the basics of what it is, how to look inside of it, and how to work with it using some of the REST APIs. Hopefully this tutorial has given you a better understanding of what Elasticsearch is and more importantly, inspired you to further experiment with the rest of its great features!