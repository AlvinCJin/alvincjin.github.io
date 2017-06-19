---
layout: post
title: Kafka Partition Leader and Controller
author: Alvin Jin
---

Kafka uses Zookeeper for the following:
* Electing a controller. The controller is one of the brokers and is responsible for maintaining the leader/follower relationship for all the partitions. Zookeeper is used to elect a controller, make sure there is only one and elect a new one it if it crashes.
* Cluster membership - which brokers are alive and part of the cluster? this is also managed through ZooKeeper.
* Topic configuration - which topics exist, how many partitions each has, where are the replicas, who is the preferred leader, what configuration overrides are set for each topic.

##Zookeeper and Controller
-----

Every time a broker process starts, it registers itself with its id in Zookeper by creating an ephemeral node. When a broker loses connectivity to Zookeeper, the ephemeral node that the broker created when starting will be automatically removed from Zookeeper. Kafka components that are watching the list of brokers will be notified for that.

The controller is one of kafka brokers is also responsible for the task of electing leaders among the partitions and replicas. The first broker starts in the cluster becomes the controller by creating an ephemeral node in Zookeeper /controller. The brokers create a Zookeeper watch on the controller node, so they get notified on changes to this node. Kafka uses Zookeeper's ephemeral node feature to elect a controller and to notify the controller when nodes join and leave the cluster.

When the controller announces that a partition has a new leader, it sends LeaderAndIsr request to the new leader and the followers.

##Request Processing
-----

All requests sent to the broker from a specific client will be processd in the order they were received. Both produce requests(producer) and fetch requests(consumers and followers) have to be sent to the leader replica of a partition.

Kafka clients uses another request "metadata request" to know where to send the requests.
Metadata request includes a list of topics the client is interested in. It can be sent to any broker since all brokers have a metadata cache taht contains this information. Clients typically cache this information, and need to occasinally refresh it by sending metadata requests.

##In-Sync Replica(ISR)
------
In order to stay in sync with the leader, the replicas send the leader Fetch requests. Only in-sync replicas are eligible to be elected as partition leaders in case the existing leader fails.

"acks" as "written successfully" when the messages was accepted by just the leader(acks=1), all in-sync replicas(acks=all). Consumers can only read messages that were written to all in-sync-replicas.