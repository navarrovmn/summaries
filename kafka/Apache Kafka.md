
# Components
___
* **Brokers*** 
	* "Servers" with their own data store. Brokers give fault tolerance to Kafka;
	* Manages partitions;
* **Producers**: insert data into Kafka cluster;
* **Consumers**: read data from Kafka cluster;
* **Zookeeper**
	* Cluster management;
	* Failure detection & recovery;
	* Store Access Control Lists (ACLs) and secrets;

![[kafka-1.png]]

## Topic, Partitions and Segments
___

![[kafka-2.png]]

### Topic
___
* Collection of related messages (sequence of events);

### Partitions
___ 
* Partitions are logs (new data is only appended, previous messages are immutable);
* Every message in the same partition is ordered;
* Data is **not** ordered per topic, but is ordered in partition;

### Consumer
___
![[kafka-4.png]]

___
![[kafka-3.png]]

## Load Balancing and Semantic Partitioning
___
* Producers use a Partitioning Strategy to assign messages to partitions;
	* Load balancing;
	* Semantic partitioning;
* Partitioning strategy is specified by producer:
	* Default strategy: `hash(key) % number_of_partitions`;
	* No key: `round robin`; 