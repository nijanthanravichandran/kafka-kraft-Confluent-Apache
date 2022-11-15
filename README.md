# kafka-kraft-Confluent-Apache

Docker compose for Confluent & Apache Kafka without zookeeper

***

# Kafka Without Zookeeper (Kraft mode):

  Zookeeper is an coordination service for many of the distributed dataprocessing frameworks. Kafka uses zookeeper for Metadata managmenet and leader election. But zookeeper has some limitations & bottle neck for the Kafka clusters with more number of nodes and partitions. 
  
  ### Limitations:
  * Kafka clusters only support limited number of partitions (Up to 200,000)
  * When a kafka broker leaves or joins a cluster high number of leader election must happen which can overload zookeeper and it slows the down the cluster
  * Metadata out of sync issue with cluster
  * High network calls between zookeeper and kafka cluster
  
   Apache Kafka Raft (KRaft) is the consensus protocol that was introduced as part KIP-500 to remove Apache Kafka’s dependency on ZooKeeper for metadata management.This greatly simplifies Kafka’s architecture by consolidating responsibility for metadata into Kafka itself, rather than splitting it between two different systems: ZooKeeper and Kafka. 
   
   KRaft mode comes with new daemon called "Quorum Controller" which replaces the role of zookeeper. This controller daemon can run along with broker or for a production grade setup, we can keep the controllers in separate machines in the kafka cluster. Minimum 3 controllers are required to achive high availability. Quorum controller use the new KRaft protocol to ensure theat metadata is accurately replicated across the quorum. Metadata is actually stored in a separate kafka topic maneged by quorum. Leader controller in the quorum handles the request and other controller within the quorum follow the acitve controller. Having metdata inside the kafka cluster itself significantly decreases the recovery time of the system. 
   
   Below metric shows the performance improvement with quorum controller:
   
   ![Screen Shot 2022-11-15 at 8 57 45 PM](https://user-images.githubusercontent.com/108142931/201980948-fa2025d9-7125-4e66-9c3e-5006c2f339c4.png)
   
### Kraft Mode Setup:
  How do you define that a broker is a controller? This is handled by a new broker setting called process.roles.  Below config in server.properties file makes the node act as broker as well as controller.
  
<img width="648" alt="Screen Shot 2022-11-15 at 9 59 58 PM" src="https://user-images.githubusercontent.com/108142931/201992697-d0a6ef48-b065-4122-b940-7b8f276a5cc9.png">

There are some downsides in keeping both broker and controller in the same node:
* Overhead for the OS to process broker log and controller log
* If there is a failure in broker due to OOM, controller will also go unavailable
* More than one broker failure makes the quorum unavailable

Best Practice:
![Screen Shot 2022-11-15 at 9 03 50 PM](https://user-images.githubusercontent.com/108142931/201994691-ae0079b5-317e-46ed-beb0-6dc0d3e7e1af.png)


> KRaft mode is marked as production ready as part of KIP-833 - Kafka Version 3.3.1


### To know more about KRaft
https://cwiki.apache.org/confluence/display/KAFKA/KIP-500%3A+Replace+ZooKeeper+with+a+Self-Managed+Metadata+Quorum
https://cwiki.apache.org/confluence/display/KAFKA/KIP-833%3A+Mark+KRaft+as+Production+Ready
https://www.confluent.io/blog/kafka-without-zookeeper-a-sneak-peek/

# Setup - Confluent Kafka
```
clone the repo
cd confluent
docker build -t nijanthan/confluentkafkakraft:7.2.1-3.2.0 .
docker compose -f docker-compose.yml up -d .
```

# Setup - Apache Kafka
```
cd apache
docker build -t nijanthan/apachekafkakraft .
docker compose -f docker-compose.yml up -d .
```


