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
这个略过(...)

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
让我们开始基础的健康状态检查，我们可以使用它来我们集群执行情况。我们将使用`curl`来做，但你也可以用其他允许你发起"HTTP/REST"请求的工具。让我们假定使用相同的节点，当我们启动Elasticsearch，还有打开另一个命令行窗口。
>Let’s start with a basic health check, which we can use to see how our cluster is doing. We’ll be using curl to do this but you can use any tool that allows you to make HTTP/REST calls. Let’s assume that we are still on the same node where we started Elasticsearch on and open another command shell window.

为了检查集群健康状态，我们将会使用`_cat`API。你可以通过点击"VIEW IN CONSOLE"使用Kibana的命令行或者使用点击"COPY AS CURL"使用`curl`命令复制粘贴到命令行终端。
>To check the cluster health, we will be using the _cat API. You can run the command below in Kibana’s Console by clicking "VIEW IN CONSOLE" or with curl by clicking the "COPY AS CURL" link below and pasting it into a terminal.
```
GET /_cat/health?v
```

响应结果：
>And the response:
```
epoch      timestamp cluster       status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1475247709 17:01:49  elasticsearch green           1         1      0   0    0    0        0             0                  -                100.0%
```

我们可以看到集群名 "elasticsearch"，是启动的绿色状态。
>We can see that our cluster named "elasticsearch" is up with a green status.

任何时候我们访问集群状态，返回都是绿色、黄色或红色。
>Whenever we ask for the cluster health, we either get green, yellow, or red.

* 绿色 - 所有服务都正常（集群全可用）
* 黄色 - 所有的数据是有效，但一些复制还没分配（集群全可用）
* 红色 - 部分数据异常（集群部分用）
>* Green - everything is good (cluster is fully functional)
>* Yellow - all data is available but some replicas are not yet allocated (cluster is fully functional)
>* Red - some data is not available for whatever reason (cluster is partially functional)

注意：当一个集群是红色状态，她会保持从有效的分片来响应查询请求的服务，但自从很多分片未被分配，你将可能需要去修复她。
>Note: When a cluster is red, it will continue to serve search requests from the available shards but you will likely need to fix it ASAP since there are unassigned shards.

同样的从上面的响应结果中，我们可以看到总共有1个节点，而且当我们没有数据时，我们有0个分片。注意从我们使用默认集群名"elasticsearch"时，或者还有从单一网络发现去默认查找其他在这个计算机上的节点。这个可能在你的计算机上意外的启动多个节点，并且他们都加入同一个集群。在这种情节中，你可能会从响应结果中看到超过1个节点。
>Also from the above response, we can see a total of 1 node and that we have 0 shards since we have no data in it yet. Note that since we are using the default cluster name (elasticsearch) and since Elasticsearch uses unicast network discovery by default to find other nodes on the same machine, it is possible that you could accidentally start up more than one node on your computer and have them all join a single cluster. In this scenario, you may see more than 1 node in the above response.

我们可以通过如下命令获取一个集群中的节点列表：
>We can also get a list of nodes in our cluster as follows:
```
GET /_cat/nodes?v
```

响应结果：
>And the response:
```
ip        heap.percent ram.percent cpu load_1m load_5m load_15m node.role master name
127.0.0.1           10           5   5    4.46                        dim      *      PB2SGZY
```
这里，我们可以看到一个节点名"PB2SGZY", 这是我们集群中当前的一个单节点。
>Here, we can see our one node named "PB2SGZY", which is the single node that is currently in our cluster.

### 列出索引目录(List All Indices)
现在让我们看一眼目录：
>Now let’s take a peek at our indices:
```
GET /_cat/indices?v
```

响应结果：
>And the response:
```
health status index uuid pri rep docs.count docs.deleted store.size pri.store.size
```

简单的表示我们集群中还没没有目录。
>Which simply means we have no indices yet in the cluster.

### 创建索引(Create an Index)
现在让我们创建一个索引并命名为"customer"，然后再一次列出所有的索引：
>Now let’s create an index named "customer" and then list all the indexes again:
```
PUT /customer?pretty
GET /_cat/indices?v
```

第一个使用 **PUT** 命令创建了索引并名为"customer"。我们简单地在请求后面附上`pretty`，让她启动`pretty-print`模块来打印JSON返回结果。
>The first command creates the index named "customer" using the PUT verb. We simply append pretty to the end of the call to tell it to pretty-print the JSON response (if any).

响应结果：
>And the response:
```
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   customer 95SQ4TSUT7mWBT7VNHH67A   1   1          0            0       260b           260b
```

第二条命令返回的这些结果告诉我们，我们已经拥有一条索引名为"customer"， 而且他有一个独立分片和一个复制（默认）和索引里面包含0个文档。
>The results of the second command tells us that we now have one index named customer and it has one primary shard and one replica (the defaults) and it contains zero documents in it.

你也许会注意到"customer"索引有一个黄色状态标记。我们前面的论述中提到黄色意味着有些复制还未分配。这个索引出现这个原因是，Elasticsearch默认值创建一个复制。从我们只有一个节点启动那一刻起，那个复制还不能很好分配（为了高可用）直到稍后的时间点，有另一个节点加入集群。一旦那个复制被分配到第二个节点时，健康状态会变成绿色。
>You might also notice that the customer index has a yellow health tagged to it. Recall from our previous discussion that yellow means that some replicas are not (yet) allocated. The reason this happens for this index is because Elasticsearch by default created one replica for this index. Since we only have one node running at the moment, that one replica cannot yet be allocated (for high availability) until a later point in time when another node joins the cluster. Once that replica gets allocated onto a second node, the health status for this index will turn to green.

### 创建索引和查询文档（Index and Query a Document）
现在让我们放一些东西到我们的"customer"索引中。我们将创建一个简单的"custoemr"文档到索引中，且ID=1，如下：
>Let’s now put something into our customer index. We’ll index a simple customer document into the customer index, with an ID of 1 as follows:
```
PUT /customer/_doc/1?pretty
{
  "name": "John Doe"
}
```

响应结果：
>And the response:
```
{
  "_index" : "customer",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

从返回中，我们可以看到一个新的"customer"文档已经成功创建在索引中。这个文档一样有一个内置ID为1和我们指定的索引一样。
>From the above, we can see that a new customer document was successfully created inside the customer index. The document also has an internal id of 1 which we specified at index time.

这个很重要的注意Elasticsearch不需要你去明确创建一个索引，在你创建索引文档进去之前。在前面的例子中，如果索引不存在是，Elasticsearch会自动创建"customer"文档的索引。
>It is important to note that Elasticsearch does not require you to explicitly create an index first before you can index documents into it. In the previous example, Elasticsearch will automatically create the customer index if it didn’t already exist beforehand.

让我们重新检索这个刚刚创建的文档：
>Let’s now retrieve that document that we just indexed:
```
GET /customer/_doc/1?pretty
```

响应结果：
>And the response:
```
{
  "_index" : "customer",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 25,
  "_primary_term" : 1,
  "found" : true,
  "_source" : { "name": "John Doe" }
}
```

这里没有什么特别的除了 `found` 字段，着显示状态我们找到一个文档且ID为1，和另一个字段：`_source`,将会返回我们上一步创建索引时的完整的JSON结果的文档信息。
>Nothing out of the ordinary here other than a field, found, stating that we found a document with the requested ID 1 and another field, _source, which returns the full JSON document that we indexed from the previous step.

### 删除索引（Delete an Index）
接下来我们来删除我们刚刚创建的索引然后列出索引的索引：
>Now let’s delete the index that we just created and then list all the indexes again:
```
DELETE /customer?pretty
GET /_cat/indices?v
```

响应结果：
>And the response:
```
health status index uuid pri rep docs.count docs.deleted store.size pri.store.size
```

这个意味着索引已经被删除成功且我们回到刚启动并且无数据在集群中。
>Which means that the index was deleted successfully and we are now back to where we started with nothing in our cluster.

在我们继续学习前，让我们回顾一遍这些我们刚刚学过的API命令：
>Before we move on, let’s take a closer look again at some of the API commands that we have learned so far:
```
PUT /customer
PUT /customer/_doc/1
{
  "name": "John Doe"
}
GET /customer/_doc/1
DELETE /customer
```
如果我们仔细的学习了上面的内容。我们可以实际的体验到一个模式来如何在Elasticsearch中存取数据。这个模式可以归纳为如下：
>If we study the above commands carefully, we can actually see a pattern of how we access data in Elasticsearch. That pattern can be summarized as follows:
```
<HTTP Verb> /<Index>/<Endpoint>/<ID>
```

这个REST存取模式可以适用所有的API命令，如果你直接记住，你掌握 Elasticsearch 将会是一个很好的开始。
>This REST access pattern is so pervasive throughout all the API commands that if you can simply remember it, you will have a good head start at mastering Elasticsearch.

## 修改数据（Modifying Your Data）
Elasticsearch提供数据操作和查询的能力近乎实时。按默认设置，你可以认为从你添加、更新、删除数据（index/update/delte）到你的结果显示有1s延迟（刷新间隔）。这是与其他平台相比很重要的区别，比如SQL，当事务完成后，数据是直接有效。
>Elasticsearch provides data manipulation and search capabilities in near real time. By default, you can expect a one second delay (refresh interval) from the time you index/update/delete your data until the time that it appears in your search results. This is an important distinction from other platforms like SQL wherein data is immediately available after a transaction is completed.

#### 创建重建文档（Indexing/Replacing Document）
我们先提前看如何添加一个简单文档的索引。再一次重复执行命令：
>We’ve previously seen how we can index a single document. Let’s recall that command again:
```
PUT /customer/_doc/1?pretty
{
  "name": "John Doe"
}
```

再一次的，上面的例子会创建一个指定的索引，即ID为1。如果我们用上面的命令执行不同的文档内容时，Elasticsearch 将会替换（或重建）为新的文档来代替原ID为1的已存在文档。
>Again, the above will index the specified document into the customer index, with the ID of 1. If we then executed the above command again with a different (or same) document, Elasticsearch will replace (i.e. reindex) a new document on top of the existing one with the ID of 1:
```
PUT /customer/_doc/1?pretty
{
  "name": "Jane Doe"
}
```

上面改变了同一个索引为1的文档对应的名字"John Doe" 为 "Jane Doe"。另外一种情况，我们使用不同的ID创建索引新的文档，而已经在索引中的已存在文档则保持不被影响。
>The above changes the name of the document with the ID of 1 from "John Doe" to "Jane Doe". If, on the other hand, we use a different ID, a new document will be indexed and the existing document(s) already in the index remains untouched.
```
PUT /customer/_doc/2?pretty
{
  "name": "Jane Doe"
}
```

上面使用ID等于2来创建了新索引文档。
>The above indexes a new document with an ID of 2.

当创建索引时，ID是可选的。 如果不指定，Elasticsearch 将会生成随机的ID并用这个ID来索引文档。实际的ID 生成后（或者像我们上一个例子那样明确指定ID），会作为结果的一半分返回。
>When indexing, the ID part is optional. If not specified, Elasticsearch will generate a random ID and then use it to index the document. The actual ID Elasticsearch generates (or whatever we specified explicitly in the previous examples) is returned as part of the index API call.

这个例子告诉我们如何添加一个索引时不需要明确的ID：
>This example shows how to index a document without an explicit ID:
```
POST /customer/_doc?pretty
{
  "name": "Jane Doe"
}
```

注意上面的例子，当我们不需要指定的ID时，我们使用 **POST** 代替 PUT 方法。
>Note that in the above case, we are using the POST verb instead of PUT since we didn’t specify an ID.

### 更新文档（Updating Documents）
为了进一步的可以创建索引和替换文档，我们可以更新文档。注意通过这种方式，实际上 Elasticsearch 不能直接更新。当我们做一次更新时，Elasticsearch 删除旧文档然后再创建了新的文档索引。
>In addition to being able to index and replace documents, we can also update documents. Note though that Elasticsearch does not actually do in-place updates under the hood. Whenever we do an update, Elasticsearch deletes the old document and then indexes a new document with the update applied to it in one shot.

这个例子示范了如何通过更改name 字段来更新前文ID为1的文档：
>This example shows how to update our previous document (ID of 1) by changing the name field to "Jane Doe":
```
POST /customer/_update/1?pretty
{
  "doc": { "name": "Jane Doe" }
}
```

这个例子示范如何通过更改name 字段的同事添加一个新的age字段来更新前文ID为1的文档：
>This example shows how to update our previous document (ID of 1) by changing the name field to "Jane Doe" and at the same time add an age field to it:
```
POST /customer/_update/1?pretty
{
  "doc": { "name": "Jane Doe", "age": 20 }
}
```

更新同样可以完美的使用简单的脚本。这个例子使用脚本来为age 字段增加5。 
>Updates can also be performed by using simple scripts. This example uses a script to increment the age by 5:
```
POST /customer/_update/1?pretty
{
  "script" : "ctx._source.age += 5"
}
```

在上面的例子中，`tx._source` 引用了当前的源文档，将会被更新。
>In the above example, ctx._source refers to the current source document that is about to be updated.

Elasticsearch 提供给定表达式来更新多个文档（比如SQL `UPDATE-WHERE`表达式), 查看[docs-update-by-query API](https://www.elastic.co/guide/en/elasticsearch/reference/7.3/docs-update-by-query.html)
>Elasticsearch provides the ability to update multiple documents given a query condition (like an SQL UPDATE-WHERE statement). See [docs-update-by-query API](https://www.elastic.co/guide/en/elasticsearch/reference/7.3/docs-update-by-query.html)

### 删除文档（Deleting Documents）
删除文档是相当简单的。这个例子示范如何删除ID为2的文档：
>Deleting a document is fairly straightforward. This example shows how to delete our previous customer with the ID of 2:
```
DELETE /customer/_doc/2?pretty
```

查看[delete_by_query API](https://www.elastic.co/guide/en/elasticsearch/reference/7.3/docs-delete-by-query.html), 来了解删除所有匹配的指定查询。值得注意的是这是一种非常高效的方式来删除整个索引代替来删除所有的文档。

>See the [delete_by_query API](https://www.elastic.co/guide/en/elasticsearch/reference/7.3/docs-delete-by-query.html) to delete all documents matching a specific query. It is worth noting that it is much more efficient to delete a whole index instead of deleting all documents with the Delete By Query API.

### 合并处理（Batch Processing）
为了进一步的创建、更新、删除个别文档，Elasticsearch 也提供了更为有效的操作来批量使用`_bluk`API。这个功能提供非常高效的原理来尽可能快地处理多个操作但尽可能更少的网络请求响应。
>In addition to being able to index, update, and delete individual documents, Elasticsearch also provides the ability to perform any of the above operations in batches using the _bulk API. This functionality is important in that it provides a very efficient mechanism to do multiple operations as fast as possible with as few network roundtrips as possible.

作为一个快速的例子，下面实例索引了两个文档（ID 1 - John Doe 与 ID 2 - Jane Doe）在一个合并操作中：
>As a quick example, the following call indexes two documents (ID 1 - John Doe and ID 2 - Jane Doe) in one bulk operation:
```
POST /customer/_bulk?pretty
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }
```

这个例子更新了第一个文档(ID=1)，并且删除了第二文档（ID=2）在一个合并操作中：
>This example updates the first document (ID of 1) and then deletes the second document (ID of 2) in one bulk operation:
```
POST /customer/_bulk?pretty
{"update":{"_id":"1"}}
{"doc": { "name": "John Doe becomes Jane Doe" } }
{"delete":{"_id":"2"}}
```

注意上面的删除动作，其中没有相应的源文档，所以删除只需要待删除文档的ID。
>Note above that for the delete action, there is no corresponding source document after it since deletes only require the ID of the document to be deleted.

合并API 不会失败因为一个动作失败而执行失败。如果一个动作不管啥原因失败，合并操作会继续处理剩下的下一个动作。当这个合并返回时，会为每个动作（用相同的排序顺序）提供一个状态，让你可以检查指定的动作是成功还是失败。
>The Bulk API does not fail due to failures in one of the actions. If a single action fails for whatever reason, it will continue to process the remainder of the actions after it. When the bulk API returns, it will provide a status for each action (in the same order it was sent in) so that you can check if a specific action failed or not.

## 探索数据（Exploring Your Data）

#### 样本数据集(Sample Dataset)
我们已经看了基础部分，让我们来尝试下更真实的数据集。我们已经为"customer"准备了一个虚构的JSON的用户银行账户信息文档样本。每个文档都有如下的数据架构。
>Now that we’ve gotten a glimpse of the basics, let’s try to work on a more realistic dataset. I’ve prepared a sample of fictitious JSON documents of customer bank account information. Each document has the following schema:
```
{
    "account_number": 0,
    "balance": 16623,
    "firstname": "Bradshaw",
    "lastname": "Mckenzie",
    "age": 29,
    "gender": "F",
    "address": "244 Columbus Place",
    "employer": "Euron",
    "email": "bradshawmckenzie@euron.com",
    "city": "Hobucken",
    "state": "CO"
}
```

有趣的是，这个数据是用[www.json-generator.com](http://www.json-generator.com/)生成的，所以请忽略实际值和数据的语义都是随机生成的。
>For the curious, this data was generated using www.json-generator.com/, so please ignore the actual values and semantics of the data as these are all randomly generated.

#### 加载样本数据集(Loading the Sample Dataset)
你可以从[这里](https://github.com/elastic/elasticsearch/blob/master/docs/src/test/resources/accounts.json?raw=true)下载样本数据集。解压到我们的当前执行命令的目录，用下列命令行执行加载。
>You can download the sample dataset (accounts.json) from here. Extract it to our current directory and let’s load it into our cluster as follows:
```
curl -H "Content-Type: application/json" -XPOST "localhost:9200/bank/_bulk?pretty&refresh" --data-binary "@accounts.json"
curl "localhost:9200/_cat/indices?v"
```

响应结果：
>And the response:
```
health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   bank  l7sSYV2cQXmu6_4rJWVIww   5   1       1000            0    128.6kb        128.6kb
```
以上意外这我们已经成功的创建了1000条文档所以到"bank"索引中。
>Which means that we just successfully bulk indexed 1000 documents into the bank index.

### 查询API（The Search API）
现在让我们从简单的查询开始。有两种基础的查询方式：一是通过REST请求地址URI中发送查询参数，另一种是把参数放在请求体body中。请求体body允许你加载更多表达式和通过JSON格式定义查询。我们将用一个请求地址发送参数的例子，但在剩余的这个教程中，我们都会使用请求体body的方式。
>Now let’s start with some simple searches. There are two basic ways to run searches: one is by sending search parameters through the REST request URI and the other by sending them through the REST request body. The request body method allows you to be more expressive and also to define your searches in a more readable JSON format. We’ll try one example of the request URI method but for the remainder of this tutorial, we will exclusively be using the request body method.

REST API 的查询通过"_search"作为末端参数。这个例子会返回所有的在"bank"索引中的文档。
>The REST API for search is accessible from the _search endpoint. This example returns all documents in the bank index:
```
GET /bank/_search?q=*&sort=account_number:asc&pretty
```

首先我们仔细分析查询请求。我们查询（_search 末端）"bank"索引，`q=*`参数命令Elasticsearch 去匹配所有在索引中的文档。`sort=account_number:asc`参数命令排序结果并用`account_number`字段进行正序排序。`pretty`参数告诉Elasticsearch 使用"pretty-printed"来输出JSON结果。
>Let’s first dissect the search call. We are searching (_search endpoint) in the bank index, and the q=* parameter instructs Elasticsearch to match all documents in the index. The sort=account_number:asc parameter indicates to sort the results using the account_number field of each document in an ascending order. The pretty parameter, again, just tells Elasticsearch to return pretty-printed JSON results.

响应结果（部分显示）：
>And the response (partially shown):
```
{
  "took" : 63,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
        "value": 1000,
        "relation": "eq"
    },
    "max_score" : null,
    "hits" : [ {
      "_index" : "bank",
      "_type" : "_doc",
      "_id" : "0",
      "sort": [0],
      "_score" : null,
      "_source" : {"account_number":0,"balance":16623,"firstname":"Bradshaw","lastname":"Mckenzie","age":29,"gender":"F","address":"244 Columbus Place","employer":"Euron","email":"bradshawmckenzie@euron.com","city":"Hobucken","state":"CO"}
    }, {
      "_index" : "bank",
      "_type" : "_doc",
      "_id" : "1",
      "sort": [1],
      "_score" : null,
      "_source" : {"account_number":1,"balance":39225,"firstname":"Amber","lastname":"Duke","age":32,"gender":"M","address":"880 Holmes Lane","employer":"Pyrami","email":"amberduke@pyrami.com","city":"Brogan","state":"IL"}
    }, ...
    ]
  }
}
```
在响应结果中，我们可以看到下面部分：
>As for the response, we see the following parts:

* took - Elasticsearch查询所花费的时间毫秒数
* timed_out - 告诉我们查询时间是否超时
* _shards - 告诉我们查询时有多事个分片。还有成功或失败的数量统计
* hits - 查询结果
* hits.total - 一个对象包含了文档匹配查询条件的总数量
    - hits.total.value - 命中查询的总量统计（必须通过`hits.total.relation`来）
    - hits.total.relation - `hits.total.value`是否是准确的命中查询值，取决于该参数是否为"eq" 或低于实际命中统计值（超过或相等），或是该参数等于"gte"
* hits.hits - 查询结果中实际的数组（默认是前10个文档）
* hits.sort - 排序值（按分值"score"排序时不生效）
* hits._score 和 max_score - 暂时忽略这些字段

>* took – time in milliseconds for Elasticsearch to execute the search
>* timed_out – tells us if the search timed out or not
>* _shards – tells us how many shards were searched, as well as a count of the successful/failed searched shards
>* hits – search results
>* hits.total – an object that contains information about the total number of documents matching our search criteria
>   - hits.total.value - the value of the total hit count (must be interpreted in the context of hits.total.relation).
>   - hits.total.relation - whether hits.total.value is the exact hit count, in which case it is equal to "eq" or a lower bound of the total hit count (greater than or equals), in which case it is equal to gte.
>* hits.hits – actual array of search results (defaults to first 10 documents)
>* hits.sort - sort value of the sort key for each result (missing if sorting by score)
>* hits._score and max_score - ignore these fields for now

准确的`hits.total`值是用另外一个请求参数`track_total_hits`控制，当这个值设置为true时，请求会跟踪命中查询的准确（"relation": "eq"）。默认设置为10,000 意味着只会命中10，000个文档。你可以通过设置`track_total_hits`为true 来禁止。 查看[请求体](https://www.elastic.co/guide/en/elasticsearch/reference/7.2/search-request-track-total-hits.html)了解更多。
>The accuracy of hits.total is controlled by the request parameter track_total_hits, when set to true the request will track the total hits accurately ("relation": "eq"). It defaults to 10,000 which means that the total hit count is accurately tracked up to 10,000 documents. You can force an accurate count by setting track_total_hits to true explicitly. See the request body documentation for more details.

这里另外一种通过请求体查询的方式：
>Here is the same exact search above using the alternative request body method:
```
GET /bank/_search
{
  "query": { "match_all": {} },
  "sort": [
    { "account_number": "asc" }
  ]
}
```

这种方法不同点在于使用请求体代替`q=*`的请求参数。Elasticsearch提供了JSON风格的查询体来使用`_search`API。我们将在下一章节讨论这种JSON查询。
>The difference here is that instead of passing q=* in the URI, we provide a JSON-style query request body to the _search API. We’ll discuss this JSON query in the next section.

明白获取查询返回的结果是很重要的，Elasticsearch是用请求完全完成，而且不需要大部分任何的服务端资源或者打开游标到返回结果中。这是强烈的对比于其他不同平台比如SQL，在这些中平台，最初可能从查询结果中预先获取一个部分子集，然后不得不继续返回服务器，如果你想使用一些服务端游标状态来获取（或者分页）剩下的返回结果。（译注：不是很明白这段话，意思是ES可以一次获取大量数据不需要分页? 还是说统计等多种信息和数据集都在一次查询返回中不需要多次查询。）
>It is important to understand that once you get your search results back, Elasticsearch is completely done with the request and does not maintain any kind of server-side resources or open cursors into your results. This is in stark contrast to many other platforms such as SQL wherein you may initially get a partial subset of your query results up-front and then you have to continuously go back to the server if you want to fetch (or page through) the rest of the results using some kind of stateful server-side cursor.

### 介绍查询语言（Introducing the Query Language）
Elasticsearch 提供了JSON风格的领域特殊语言让我们可以施工用执行查询。关于[Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/7.2/query-dsl.html)。这种查询语言相当综合而且乍一看很吓人，但是是很好的方式通过实际来学习,让我们开始一个简单的例子。

>Elasticsearch provides a JSON-style domain-specific language that you can use to execute queries. This is referred to as the Query DSL. The query language is quite comprehensive and can be intimidating at first glance but the best way to actually learn it is to start with a few basic examples.

回到我们上一个例子，我们执行下面的查询：
>Going back to our last example, we executed this query:
```
GET /bank/_search
{
  "query": { "match_all": {} }
}
```

仔细分析上面的例子，`query`部分告诉我们什么是查询的定义, `match_all`部分是简单的查询类型是我们要执行的。`match_all`查询是在制定的索引（"bank"）中，简单的查询全部文档。
>Dissecting the above, the query part tells us what our query definition is and the match_all part is simply the type of query that we want to run. The match_all query is simply a search for all documents in the specified index.

另外的查询参数，我们可以通过其他参数来影响查询结果。在前面的例子中我们传入`sort`,这里我们传入`size`:
>In addition to the query parameter, we also can pass other parameters to influence the search results. In the example in the section above we passed in sort, here we pass in size:
```
GET /bank/_search
{
  "query": { "match_all": {} },
  "size": 1
}
```

注意如果不指定`size`,默认是10
>Note that if size is not specified, it defaults to 10.

这个例子使用`match_all`且返回了从10到19的文档。
>This example does a match_all and returns documents 10 through 19:
```
GET /bank/_search
{
  "query": { "match_all": {} },
  "from": 10,
  "size": 10
}
```

`from`参数（从0起始）指定那个文档索引从开始到`size`参数指定的多少个文档来返回从`from`参数。这个特性是非常有用在需要分页获取返回结果时。注意如果`from`不指定，默认值是0。
>The from parameter (0-based) specifies which document index to start from and the size parameter specifies how many documents to return starting at the from parameter. This feature is useful when implementing paging of search results. Note that if from is not specified, it defaults to 0.

这个例子使用`match_all`且使用"account"字段倒序排序了结果，并返回了前10个（默认大小）结果。
>This example does a match_all and sorts the results by account balance in descending order and returns the top 10 (default size) documents.
```
GET /bank/_search
{
  "query": { "match_all": {} },
  "sort": { "balance": { "order": "desc" } }
}
```

### 执行查询（Executing Searches）
现在我们已经看过基础的查询参数，让我们深挖更多的Query DSL。首先让我们看一眼返回文档字段。默认的，全部JSON文档作为返回结果的一部分。（`_source`）。如果我们不想要整个源文档返回，我们有能力只请求返回少量包含在源文档中的字段。
>Now that we have seen a few of the basic search parameters, let’s dig in some more into the Query DSL. Let’s first take a look at the returned document fields. By default, the full JSON document is returned as part of all searches. This is referred to as the source (_source field in the search hits). If we don’t want the entire source document returned, we have the ability to request only a few fields from within source to be returned.

这个例子示范了如何只返回两个字段，"account_number"和"balance"(包含在 `_source` 里面)的的查询：
>This example shows how to return two fields, account_number and balance (inside of _source), from the search:
```
GET /bank/_search
{
  "query": { "match_all": {} },
  "_source": ["account_number", "balance"]
}
```
注意上面列子简单减少`_source`字段，她也是一样只返回一个`_source`字段，但内部只包含有一个`account_number`和`balance`。
>Note that the above example simply reduces the _source field. It will still only return one field named _source but within it, only the fields account_number and balance are included.

如果你来自SQL后端，上面多少有些跟SQL 的SELECT FROM 的字段列表概念相似。
>If you come from a SQL background, the above is somewhat similar in concept to the SQL SELECT FROM field list.

现在让我们移到查询部分。在前面，我们看到如何`match_all`查询是用户匹配所有文档。现在让我们介绍一种新的查询叫`match`查询，可以作为基础的查询字段(比如：一个查询完成指定一个字段或者一个数据集的字段)。
>Now let’s move on to the query part. Previously, we’ve seen how the match_all query is used to match all documents. Let’s now introduce a new query called the match query, which can be thought of as a basic fielded search query (i.e. a search done against a specific field or set of fields).

这个例子返回`account_number` 值为20的：
>This example returns the account numbered 20:
```
GET /bank/_search
{
  "query": { "match": { "account_number": 20 } }
}
```

这个例子返回所有账户包含匹配"mill"值在`address`中：
>This example returns all accounts containing the term "mill" in the address:
```
GET /bank/_search
{
  "query": { "match": { "address": "mill" } }
}
```

这个例子返回所有账户包含匹配"mill"或是"lane"值在`address`中：
>This example returns all accounts containing the term "mill" or "lane" in the address:
```
GET /bank/_search
{
  "query": { "match": { "address": "mill lane" } }
}
```

这个例子是不同的`match`(`match_phrase`)会返回所有账户中包含了短语"mill lane"值在`address`中：
>This example is a variant of match (match_phrase) that returns all accounts containing the phrase "mill lane" in the address:
```
GET /bank/_search
{
  "query": { "match_phrase": { "address": "mill lane" } }
}
```

现在是时候介绍`bool`查询。`bool`查询允许我们使用布尔逻辑构成小查询到大查询。
>Let’s now introduce the bool query. The bool query allows us to compose smaller queries into bigger queries using boolean logic.

这个例子构成两个`match`查询且返回所有账户包含了"mill"且"lane"值在`address`中：
>This example composes two match queries and returns all accounts containing "mill" and "lane" in the address:
```
GET /bank/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}
```
在上面的例子中，`bool`的`must`子句指定所有查询必须都为true，对于任一个文档都必须被匹配。
>In the above example, the bool must clause specifies all the queries that must be true for a document to be considered a match.

在对比下，这个例子构成两个`match`查询且返回所有账户包含了"mill"或"lane"值在`address`中：
>In contrast, this example composes two match queries and returns all accounts containing "mill" or "lane" in the address:
```
GET /bank/_search
{
  "query": {
    "bool": {
      "should": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}
```

在上面的例子中，`bool`的`should`子句指定查询列表至少有一个为true,对于任一个文档都必须被匹配。
>In the above example, the bool should clause specifies a list of queries either of which must be true for a document to be considered a match.

这个例子构成两个`match`查询且返回所有账户都不包含了"mill"或"lane"值在`address`中：
>This example composes two match queries and returns all accounts that contain neither "mill" nor "lane" in the address:
```
GET /bank/_search
{
  "query": {
    "bool": {
      "must_not": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}
```

在上面的例子中，`bool`的`must_not`子句指定查询列表任一个都不为true,对于任一个文档都必须被匹配。
>In the above example, the bool must_not clause specifies a list of queries none of which must be true for a document to be considered a match.

我们可以结合`must`,`should`和``must_not`子句同时在一个`bool`查询中。此外，我们可以构成`bool`查询进入任一个`bool`子句去模仿任何一个复杂的多级布尔逻辑。
>We can combine must, should, and must_not clauses simultaneously inside a bool query. Furthermore, we can compose bool queries inside any of these bool clauses to mimic any complex multi-level boolean logic.

这个例子返回所有账号中age等于40，但是不住在ID（aho）：
>This example returns all accounts of anybody who is 40 years old but doesn’t live in ID(aho):
```
GET /bank/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "age": "40" } }
      ],
      "must_not": [
        { "match": { "state": "ID" } }
      ]
    }
  }
}
```

### 执行过滤（Executing Filters）
在前面的小节中，我们跳过一些详细的文档评分（查询结果中的`_score`字段）。这个分数是数值，是文档匹配查询指定值的相关性如何。在更高的分数，则文档相关更大；更低的评分，则文档相关性更小。
>In the previous section, we skipped over a little detail called the document score (_score field in the search results). The score is a numeric value that is a relative measure of how well the document matches the search query that we specified. The higher the score, the more relevant the document is, the lower the score, the less relevant the document is.

但查询不会一直需要生成评分，在个别只需要过滤的文档集中。Elasticsearch 发现这些情景和自动优化查询执行在排序不会去计算无用的评分。
>But queries do not always need to produce scores, in particular when they are only used for "filtering" the document set. Elasticsearch detects these situations and automatically optimizes query execution in order not to compute useless scores.

在我们前面介绍的`bool`查询中一样支持`filter`子句，她允许我们使用查询去重新约束文档，会被其他子句匹配，但已经计算完成不改变如何排序。在例子中我们介绍`range`查询，可以运行我们过滤掉文档中某些范围的值。这个适用于数值和日期过滤。
>The bool query that we introduced in the previous section also supports filter clauses which allow us to use a query to restrict the documents that will be matched by other clauses, without changing how scores are computed. As an example, let’s introduce the range query, which allows us to filter documents by a range of values. This is generally used for numeric or date filtering.

这个例子使用`bool`查询去返回账号中"balances"在20000到30000之间的，包含在内。换而言之，我们想查账户中"balance"字段大于或等于20000且低于等于30000。
>This example uses a bool query to return all accounts with balances between 20000 and 30000, inclusive. In other words, we want to find accounts with a balance that is greater than or equal to 20000 and less than or equal to 30000.
```
GET /bank/_search
{
  "query": {
    "bool": {
      "must": { "match_all": {} },
      "filter": {
        "range": {
          "balance": {
            "gte": 20000,
            "lte": 30000
          }
        }
      }
    }
  }
}
```
仔细分析上面的例子，`bool`查询包含一个`match_all`查询（查询部分）和一个`range`范围查询（过滤部分）。我们可以代替其他的查询语句到查询部分和过滤部。在上面的例子中，范围查询非常的有意义，在范围中的所有的文档都匹配“相等”，比如，没有文档比其他的更有相关性。
>Dissecting the above, the bool query contains a match_all query (the query part) and a range query (the filter part). We can substitute any other queries into the query and the filter parts. In the above case, the range query makes perfect sense since documents falling into the range all match "equally", i.e., no document is more relevant than another.

另外对于`match_all`,`match`,`bool`和`range`查询，这里有很多其他查询非常有效，我们咱不会在这里深入。从我们已经基本理解她如何工作，就不会太难去应用这些已经学习的知识和尝试其他的查询类型。
>In addition to the match_all, match, bool, and range queries, there are a lot of other query types that are available and we won’t go into them here. Since we already have a basic understanding of how they work, it shouldn’t be too difficult to apply this knowledge in learning and experimenting with the other query types.

### 执行聚合（Executing Aggregations）
聚合提供从数据中分组能力和提取统计。更早方式思考聚合是大概的类比等于SQL 的GROUP BY 语句和SQL的聚类函数。在Elasticsearch中，你有能力执行查询返回命中结果，同时返回聚合结果单独从命中所有都在一个响应结果中。这个非常强大高效的，你可以运行查询和多种聚合，还有使用简洁又简单的API来同时（或其中一个）操作获取结果，在一次请求中避免网络多次响应。
>Aggregations provide the ability to group and extract statistics from your data. The easiest way to think about aggregations is by roughly equating it to the SQL GROUP BY and the SQL aggregate functions. In Elasticsearch, you have the ability to execute searches returning hits and at the same time return aggregated results separate from the hits all in one response. This is very powerful and efficient in the sense that you can run queries and multiple aggregations and get the results back of both (or either) operations in one shot avoiding network roundtrips using a concise and simplified API.

首先，这个例子用"state"分组了所有的账号, 然后返回前10个（默认值）根据统计数倒序排序（默认值）。
>To start with, this example groups all the accounts by state, and then returns the top 10 (default) states sorted by count descending (also default):
```
GET /bank/_search
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword"
      }
    }
  }
}
```
在SQL中，上面的聚合概念类似于下面：
>In SQL, the above aggregation is similar in concept to:
```
SELECT state, COUNT(*) FROM bank GROUP BY state ORDER BY COUNT(*) DESC LIMIT 10;
```

响应结果（部分显示）：
>And the response (partially shown):
```
{
  "took": 29,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped" : 0,
    "failed": 0
  },
  "hits" : {
     "total" : {
        "value": 1000,
        "relation": "eq"
     },
    "max_score" : null,
    "hits" : [ ]
  },
  "aggregations" : {
    "group_by_state" : {
      "doc_count_error_upper_bound": 20,
      "sum_other_doc_count": 770,
      "buckets" : [ {
        "key" : "ID",
        "doc_count" : 27
      }, {
        "key" : "TX",
        "doc_count" : 27
      }, {
        "key" : "AL",
        "doc_count" : 25
      }, {
        "key" : "MD",
        "doc_count" : 25
      }, {
        "key" : "TN",
        "doc_count" : 23
      }, {
        "key" : "MA",
        "doc_count" : 21
      }, {
        "key" : "NC",
        "doc_count" : 21
      }, {
        "key" : "ND",
        "doc_count" : 21
      }, {
        "key" : "ME",
        "doc_count" : 20
      }, {
        "key" : "MO",
        "doc_count" : 20
      } ]
    }
  }
}
```

我们可以看到总27个账号在ID（Idaho），接着27个账号在TX（Texas），接着25个账号在AL（Alabama）等等。
>We can see that there are 27 accounts in ID (Idaho), followed by 27 accounts in TX (Texas), followed by 25 accounts in AL (Alabama), and so forth.

注意我们设置`site = 0`将不显示查询命中，因为我们只在响应结果中想看到聚合的结果。
>Note that we set size=0 to not show search hits because we only want to see the aggregation results in the response.

构建前面的聚合，这个例子通过state计算平均账户的"balance"(再一次只为前10个根据统计值倒序排序):
>Building on the previous aggregation, this example calculates the average account balance by state (again only for the top 10 states sorted by count in descending order):
```
GET /bank/_search
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword"
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}
```

注意我们嵌套了`average_balance`到`group_by_state`聚合中。这是对于所有的聚合来说是一个普通的模式。你可以嵌套聚合到任意的聚合来提取提供统计来满足从数据获取的请求。
>Notice how we nested the average_balance aggregation inside the group_by_state aggregation. This is a common pattern for all the aggregations. You can nest aggregations inside aggregations arbitrarily to extract pivoted summarizations that you require from your data.

构建一个前面的聚合。让我们现排序"balance"的平均值并倒序：
>Building on the previous aggregation, let’s now sort on the average balance in descending order:
```
GET /bank/_search
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword",
        "order": {
          "average_balance": "desc"
        }
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}
```

这个例子展示如何分组年龄登记（20-29,30-39,40-49）,然后按性别，最终获得平均的账户"balance",年龄段百分百，性别百分比：
>This example demonstrates how we can group by age brackets (ages 20-29, 30-39, and 40-49), then by gender, and then finally get the average account balance, per age bracket, per gender:
```
GET /bank/_search
{
  "size": 0,
  "aggs": {
    "group_by_age": {
      "range": {
        "field": "age",
        "ranges": [
          {
            "from": 20,
            "to": 30
          },
          {
            "from": 30,
            "to": 40
          },
          {
            "from": 40,
            "to": 50
          }
        ]
      },
      "aggs": {
        "group_by_gender": {
          "terms": {
            "field": "gender.keyword"
          },
          "aggs": {
            "average_balance": {
              "avg": {
                "field": "balance"
              }
            }
          }
        }
      }
    }
  }
}
```

有很多其他的聚合我们暂不详细展开，
[aggregations reference guide](https://www.elastic.co/guide/en/elasticsearch/reference/7.2/search-aggregations.html) 是非常好的起点，如果你想尝试更多。
>There are many other aggregations capabilities that we won’t go into detail here. The aggregations reference guide is a great starting point if you want to do further experimentation.


## 总结（Conclusion）
Elasticsearch 是即简单又复杂的产品。我们到目前为止学习了关于 Elasticsearch 的基础，及如何查看和使用 REST APIs。希望这个教程能给你更好的理解 Elasticsearch 是什么，且更重要的是激发你未来去探索 Elastissearch 更丰富的功能。
>Elasticsearch is both a simple and complex product. We’ve so far learned the basics of what it is, how to look inside of it, and how to work with it using some of the REST APIs. Hopefully this tutorial has given you a better understanding of what Elasticsearch is and more importantly, inspired you to further experiment with the rest of its great features!
