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
And the response:
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

### List All Indices
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

### Create an Index
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

### Index and Query a Document
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

让我骂你重新检索这个刚刚创建的文档：
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

### Delete an Index
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

#### Indexing/Replacing Documentsedit
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

### Updating Documents
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

### Deleting Documents
删除文档是相当简单的。这个例子示范如何删除ID为2的文档：
>Deleting a document is fairly straightforward. This example shows how to delete our previous customer with the ID of 2:
```
DELETE /customer/_doc/2?pretty
```

查看[delete_by_query API](https://www.elastic.co/guide/en/elasticsearch/reference/7.3/docs-delete-by-query.html), 来了解删除所有匹配的指定查询。值得注意的是这是一种非常高效的方式来删除整个索引代替来删除所有的文档。

>See the [delete_by_query API](https://www.elastic.co/guide/en/elasticsearch/reference/7.3/docs-delete-by-query.html) to delete all documents matching a specific query. It is worth noting that it is much more efficient to delete a whole index instead of deleting all documents with the Delete By Query API.

### Batch Processing
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
todo...
### The Search API
todo...
### Introducing the Query Language
todo...
### Executing Searches
todo...
### Executing Filters
todo...
### Executing Aggregations
todo...

## 总结（Conclusion）
Elasticsearch 是即简单又复杂的产品。我们到目前为止学习了关于 Elasticsearch 的基础，及如何查看和使用 REST APIs。希望这个教程能给你更好的理解 Elasticsearch 是什么，且更重要的是激发你未来去探索 Elastissearch 更丰富的功能。
>Elasticsearch is both a simple and complex product. We’ve so far learned the basics of what it is, how to look inside of it, and how to work with it using some of the REST APIs. Hopefully this tutorial has given you a better understanding of what Elasticsearch is and more importantly, inspired you to further experiment with the rest of its great features!
