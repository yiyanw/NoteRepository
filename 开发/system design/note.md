# System Design

# Fundamentals

* key characteristics for system design
* actual components

## Client-Server Model

* process
  1. DNS - get IP by host name
  2. send http request to target IP with specific port number
  3. server receive the http request
  4. server reply the data that client wants

## Network Protocols

* IP
* TCP
* HTTP
* 给了一个http的demo

## Storage

* Disk - persist
* Memory - not persist

## Latency and Throughput

* Latency - how it take data from one point of system to other point of system
  * read data from memory - fastest
  * read data from ssd
  * read data from 1Gbps network 
  * read data from HDD
  * send packets from CA -> Netherland -> CA slowest - slowest
  * 需要low latency的application
    * video game
* Throughput - how much data can be transferred from one point of system to other point in a given amount of time 
  * unit - e.g. GB per sec
* latency and throughput are not correlated

## Availability 

* SLA/SLO
  * Service Level Agreement - what availability is guaranteed for clients
    * SLA are made up of one or more SLOs
    * [AWS SLA](https://aws.amazon.com/legal/service-level-agreements/?aws-sla-cards.sort-by=item.additionalFields.serviceNameLower&aws-sla-cards.sort-order=asc&awsf.tech-category-filter=*all)
  * Service Level Objectives - percentage of up time
    * google cloud 提供 99.999%
* Nines - High Availability 的标准
  * two nines 99% - 3.65 days per year
  * three nines 99.9% - 8.77 hours per year
  * 需要具体分析哪个部分的HA更重要
* Redundancy 冗余
  * eliminate single point of failures
  * double even triple the core system component
    * what if load balancer failed? - more load balancers required - passive redundancy
    * active redundancy - second server only works when the main server failed
  * proper process to handle failures

## Caching

* database cache
* time consuming request cache
* client cache - browser cache?
* possibility of staleness for distributed system
* think about eviction policies - how to get of the old cache
  * the policy by which values get evicted or removed from a cache. 
  * popular eviction policy 
    * LRU Least-recently used
    * FIFO first in first out
    * LFU least frequently used
* cache hit - when requested data is found in a cache
* cache miss - when requested data could have been found in a cache but isn't.
  * when old server goes down, load balancer need to forward requests to new server, which will result in cache misses.
* CDN content delivery network
  * most popular cdn is cloudflare and google cloud cdn



## Proxies

* forward proxy
  * (client - forward proxy) - server
  * client's ip address was hided from servers
  * for example - VPN
  * act on client side
* reverse proxy
  * client - (reverse proxy - server)
  * act on server side
  * server's ip address was hided from clients
  * for example - nginx
  * used for
    * filtering requests
    * load balancer
    * static resources

## Load Balancer

* round robin - 所有服务器依次分配requests
* weighted round robin - 可以分配不同服务器分配requests的比重
* 按照path区分server - payment server, search server, xxx server
  * server selection strategy

## Hashing

* a function that takes in a specific data type and outputs a number. Different inputs may have the same output, but a good hashing function attempts to minimize those hashing collisions
  * path based load balancer
  * this can be used for fixing requests from same client to same server
  * need to handle the scenarios of adding or removing server
    * consistent Hashing - minimizing the number of keys that need to be remapped when a hash table gets resized
      * hash function 保持不变，每个server handle一个interval范围内的requests
      * 比如hash function 的output范围在0-100，那么ABCD分别处理0-25, 26-50,51-75,76-100的request
        * 如果C挂了，那么那么D会承担51-100的request。
        * request会去找比hash output大的第一个server
    * rendezvous hashing -  a type of hashing also coined highest random weight hashing. Allows for minimal re-distribution of mappings when a server goes down.
      * 为每个server计算分数，always pick up the highest one
  
* SHA - secure hash algorithms, which is a collection of cryptographic hash functions used in the industry. 

## Relational Database

* 2 core things: **record** data and **query** data.
  * they are usually are individual servers that are long lived
  * interacted with application by TCP and HTTP
  
* Disk

  * 2 types: HDD hard-disk drive and SSD solid-state drive

* Memory

  * Random Access Memory (RAM)
  * will lose after the process dies

* Relational Database

  * store data in a tabular format
  * can use SQL to query data

* Non-Relational Database

  * NoSQL Databases

* SQL 

  * structured Query Language

* ACID Transaction

  * Atomicity

  * Consistency

    * strong consistency - the transaction cannot bring the database to an invalid state. After the transaction is committed or rolled back, the rules for each record will still apply, and all future transactions will see the effect of the transaction.

    * the opposite is eventual consistency: querying operations may read some stale data. However, the eventual consistency will give guarantees that the state of the database will eventually reflect writes within a time period.

  * Isolation

  * Duration

## Key-Value Stores

* for less latency

* NoSQL database
  * ETCD
  * Redis
  * ZooKeeper

## Specialized Storage Paradigms

* Blob Store
  * used to storage unstructured data
  * GCS
  * S3
  * **different with key-value store,** they focus on different aspects
    * key-value store mainly focuses on latency
    * blob store mainly focuses on availability and duration
* Time Series DB
  * InfluxDB
  * Prometheus
* Graph DB
  * A type of database that stores data following the graph data model. Data entries in a graph database can have explicitly defined relationships, much like nodes in a graph can have edges.
  * Dealing with systems where data points naturally form a graph and have multiple levels of relationships
    * for example, social networks.
  * Cypher - a graph query language
  * Neo4j
* Spatial DB
  * a type of database optimized for storing and querying spatial data like locations on a map. Spatial databases rely on spatial indexes like quadtrees to quickly perform spatial queries like finding all locations in the vicinity 附近 of a region.
  * Quadtree

## Replication and Sharding

* replication
  * High availability
    * duplicated version of database
    * same as main database
    * replica will take over when main database goes down
  * reduce latency
    * multi databases in different locations
    * databases need to synchronize data to other database
      * this update could be asynchronized (e.g. every 10 minutes)
  * improve performance for larger scale request
* sharding
  * store different part of data in different databases
  * usually use reverse-proxy
  * how to split
    * split table
      * e.g. name start with A-E on database1, F-J on database2
      * need to avoid hotspot （some databases have more requests）

## Leader Election

* 在第三方服务和数据库之间，通常会有proxy cluster，如何在当前main proxy崩了的时候选一个新的proxy出来。

* The process by which nodes in a cluster elect a so-called "leader" amongst them, responsible for the primary operations of the service that these nodes support. When correctly implemented, leader election guarantees that all nodes in the cluster know which one is the leader at any given time and can elect a new leader if the leader dies for whatever reason.

* Consensus Algorithm

  *  a type of complex algorithms used to have multiple entities agree on a single data value, like who the "leader" is amongst a group of machines. 

  * Two popular consensus aglorithms:

    * paxos

    * raft

  * also can directly use zookeeper and ETCD to implement leader election

## Peer-to-peer Network

* a collection of machines referred to as peers that divide a workload between themselves to presumably complet the workload faster than would otherwise be possible. Peer-to-peer networks are often used in file-distribution systems.
* peer selection
  * method1 - create a central database to indicate every peer which peer should be talked to next.
  * method2 Distributed Hash Table DHT - each peer updates a hash table with ip and chunks that peer has.

* Kraken
* Gossip Protocol
  * when a set of machines talk to each other in a uncoordinated manner in a cluster to spread information through a system without requiring a central source of data.

## Polling And Streaming

* polling
  * the act of fetching a resource or piece of data regularly at an interval to make sure your data is not too stale
  * for less frequent scenario
* streaming
  * In networking, it usually refers to the act of continuously getting a feed of information from a server by keeping an open connection between the two machines or processes.
  * for every frequent scenario (usually less than a second per request)
  * WebSocket

## Configuration

* static - 需要重启服务器 修改才能生效
* dynamic
* usually json or yaml file

## Rate Limiting

* rate limiting - limiting the number of requests sent to or from a system. Rate limiting is most often used to limit the number of incoming requests in order to prevent DoS attacks and can enforced at the IP-address level, at the user-account level, or at the region level.
* DoS Attack - denial-of-service - a malicious user tries to bring down or damage a system in order to render it unavailable to users. Much of the time, it consists of flooding it with traffic. Some DoS attacks are easily preventable with rate limiting, while others can be far trickier to defend against.
* DDoS Attack - Distributed Denial-of-service - a DoS attack in which the traffic flooding the target system comes from many different sources, making it much harder to defend against.

## Logging and Monitoring

* logging - the act of collecting and storing logs including useful information about events in system. 
* monitoring - the process of having visibility into a system's key metrics, monitoring is typically implemented by collecting important events in a system and aggregating them in human-readable charts.
* alerting - the process through which system administrators get notified when critical system issues occur. Alerting can be set up by defining specific thresholds on monitoring charts, past which alerts are sent to a communication channel like Slack.

## Publish/Subscribe Pattern

* pub/sub pattern - a popular messaging model that consists of publishers and subscribers. Publishers publish messages to special topics without caring about or even knowing who will read those messages, and subscribers subscribe to topics and read messages coming through those topics.
  * guarantees 
    * at least once delivery
    * persistent storage
    * ordering of message
    * replayability of message
* Idempotent Operation
  * multi times operations will not change the overall effect.

* kafka
* google cloud pub/sub

## MapReduce

* MapReduce
  * a programming model for processing and generating big data sets with a parallel, distributed algorithm on a cluster
  * **map step** - runs map function on the various chunks of the dataset and transforms these chunks into intermediate key-value pairs.
  * shuffle step - reorganize the intermediate key-value pairs
  * **reduce step** - runs reduce function on key-value pairs

* File System - an abstraction over a storage medium that defines how to manage data. While there exist many different types of file systems, most follow a hierarchical structure that consists of directories and files, like Unix file system.
* Distributed File System - takes care of the classic availability and replication guarantees that can be tricky to obtain in a distributed-system setting. 

## Security and HTTPS

* http - 
  * Man In The Middle Attack - MITM attack
* encryption - 
  * symmetric - 1 key - faster
    * AES - advanced encryption standard
  * asymmetric - pair key - slower
* https - 
  * TLS - transport layer security
  * SSL - secure socket layer

## API Design

* some examples:
  * Stripe API
  * Google Cloud IoT Core API
  * tweeter API

# How to clarify the question

1. clarify what should I need to design in detail (in API level or function level)
   1. do I need to implement it in detail (algorithm or logic)
   2. any other auxiliary services?
2. throughput 
   1. how many clients?
   2. how many requests per day?
   3. regional information?
3. availability

system design

how to work in a team
During my work at Huawei, I had the opportunity to work in a professional group and I learnt  good time management and communication skill.

At the beginning of each iteration, our team leader would assign tasks with brief descriptions. It was crucial for me to seek clarification, predict a time schedule, and then submit it to the leader for review. In order to handle unexpected challenges, I always set the deadline few days earlier than group deadline. This was quite important, because we always had some unexpected troubles, such as urgent bugs or delays from other team members. By having this extra time, I was well-prepared to provide support and assistance when such situations arose.

在每个迭代的开始，我们组长会给每个人分配任务。通常这个任务不会太详细，所以我需要自己澄清需求并做好时间计划。然后将这些整理好提交给组长用于检查是否合理。为了能够能够处理许多意想不到的问题，我通常会将deadline设置在迭代deadline前的几天。因为我们常常会遇到突发事件，比如urgent bugs，task delay，这么做最大的好处是我可以在这些事情发生时，总是能够提供支持和帮助。

On the other hand, Regularly updating my progress to the team leader and seeking feedback was another aspect of my approach. Usually, development tasks were often disrupted by urgent requirements, it was crucial for me to inform my leader promptly if I required assistance or if I could offer support to others. 

另一方面，定期和组长汇报工作和获取反馈也很重要。因为突发事件常常发生，我需要让我的组长知道我何时需要帮助，以及我是否能够提供帮助。

this time management skill and communication helped me a lot in my work.

technology

previous experience



