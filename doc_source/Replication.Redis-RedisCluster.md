# Replication: Redis \(cluster mode disabled\) vs\. Redis \(cluster mode enabled\)<a name="Replication.Redis-RedisCluster"></a>

Beginning with Redis version 3\.2, you have the ability to create one of two distinct types of Redis clusters \(API/CLI: replication groups\)\. A Redis \(cluster mode disabled\) cluster always has a single shard \(API/CLI: node group\) with up to 5 read replica nodes\. A Redis \(cluster mode enabled\) cluster has up to 15 shards with 1 to 5 read replica nodes in each\.

![\[Image: Redis (cluster mode disabled) and Redis (cluster mode enabled) clusters\]](http://docs.aws.amazon.com/AmazonElastiCache/latest/UserGuide/images/ElastiCache-NodeGroups.png)

*Redis \(cluster mode disabled\) and Redis \(cluster mode enabled\) clusters*

The following table summarizes important differences between Redis \(cluster mode disabled\) and Redis \(cluster mode enabled\) clusters\.


**Comparing Redis \(cluster mode disabled\) and Redis \(cluster mode enabled\) Clusters**  

| Feature | Redis \(cluster mode disabled\) | Redis \(cluster mode enabled\) | 
| --- | --- | --- | 
| Modifiable | Yes\. Supports adding and deleting replica nodes, and scaling up node type\. | Limited\. Version 3\.2\.10 can add and remove shards dynamically\. Other changes require creating a new cluster\. | 
| Data Partitioning | No | Yes | 
| Shards | 1 | 1 to 15 The number of shards \(API/CLI: node groups\) is set when the cluster \(API/CLI: replication group\) is created\. | 
| Read replicas | 0 to 5 If you have no replicas and the node fails, you will experience total data loss\. | 0 to 5 per shard\. | 
| Multi\-AZ with Automatic Failover | Yes, with at least 1 replica\. Optional\. On by default\. | Yes\. Required\. | 
| Snapshots \(Backups\) | Yes, creating a single \.rdb file\. | Yes, creating a unique \.rdb file for each shard\. | 
| Restore | Yes, using a single \.rdb file\. | Yes\. You can resize your cluster when restoring\. | 
| Supported by | All Redis versions | Redis 3\.2 and following | 
| Engine upgradeable | Yes | N/A | 
| Encryption | No\. | Version 3\.2\.6 only | 
| HIPAA Compliant | No | Version 3\.2\.6 only | 

## Which should I choose?<a name="Replication.Redis-RedisCluster.Choose"></a>

When choosing between Redis \(cluster mode disabled\) or Redis \(cluster mode enabled\) you should consider the following factors:

+ **Scaling v\. Partitioning** – Business needs change\. You need to either provision for peak demand or scale as demand changes\. Redis \(cluster mode disabled\) supports scaling\. You can scale read capacity by adding or deleting replica nodes, or you can scale capacity by scaling up to a larger node type\. Both of these operations take time\. For more information, see [Scaling Redis \(cluster mode disabled\) Clusters with Replica Nodes](Scaling.RedisReplGrps.md)\.

   

  Redis \(cluster mode enabled\) supports partitioning your data across up to 15 node groups\. You can dynamically change the number of shards as your business needs change\. One advantage of partitioning is that you spread your load over a greater number of endpoints which reduces access bottle necks during peak demand\. Additionally, you can accommodate a larger data set since the data can be spread across multiple servers\. For information on scaling your partitions, see [Scaling for Amazon ElastiCache for Redis—Redis \(cluster mode enabled\)](scaling-redis-cluster-mode-enabled.md)\.

   

+ **Node size v\. Number of nodes** – Because a Redis \(cluster mode disabled\) cluster has only one shard, the node type must be large enough to accommodate all the cluster's data plus necessary overhead\. On the other hand, because you can partition your data across several shards when using a Redis \(cluster mode enabled\) cluster, the node types can be smaller, though you will need more of them\. For more information, see [Choosing Your Node Size for Redis Clusters](CacheNodes.SelectSize.md#CacheNodes.SelectSize.Redis)\.

   

+ **Reads v\. Writes** – If the primary load on your cluster is applications reading data, you can scale a Redis \(cluster mode disabled\) cluster by adding and deleting read replicas, though you should note that there is a maximum of 5 read replicas\. If the load on your cluster is write heavy, you can benefit from the additional write endpoints of a Redis \(cluster mode enabled\) cluster with multiple shards\.

Whichever type of cluster you choose to implement, be sure to choose a node type that is adequate for your current and future needs\.