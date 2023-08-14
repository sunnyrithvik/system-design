# II. Databases

## 1. Relational Databases

Relational Databases
- History of relational db
- key properties are
	1. Data consistency
	2. Data durability
	3. Data integrity
	4. Constraints
	5. Everything in on place

#### Transactions
ACID 
- Atomicity - all statements within a transaction takes effect or none
- Consistency - data will never go incorrect, no matter what constraints, cascades, triggers
- Isolation - when multiple transactions are executing parallely, the isolation level determines how much changes of one transaction are visible to other
- Durability - when transaction commits, the changes outlives outage

---

## 2. Database Isolation levels

Isolation levels dictate how much one transaction knows about the other
a. Repeatable reads
	- consistent reads within same transaction
		even if other transaction committed 1st transaction would not see the changes (if value already read)

b. Read committed
	- reads within the same transaction always reads fresh value
		con: multiple reads within same transaction are inconsistent

c. Read uncommitted
	- reads every uncommitted values from other transactions
		"dirty reads"

d. Serializable
	- every read is a locking read (depends on engine)
	and while one transaction read, other transactions will have to wait

* storage engines can alter the implementation

--------------------------------------
3. Scaling Databases

 These techniques are applicable to most databases out there 
 (both relational and non-relational)

 a. Vertical scaling
 	- add more cpu, ram, disk to the DB
 	- requires downtime during reboot
 	- gives you ability to handle "scale", more load
 	- vertical scaling has a physical hardware limitation

 b. Horizantal scaling

 	I.Read Replicas
 		- when read:write = 90:10
 		- you move reads to other db so that "master" is free to do writes
 				sync/async
api --->master --------------> replica
 				replication

 		- api servers should know which db to connect to get things done

 	Replication 
 		- changes on one database (master) needs to be sent to replica to maintain consistency
 		two modes of replication
 		1. Sync replication
 				w 		  w             w
 			------->   ------->		 ------->
 		user		API 	   Master		Replica
 			<-------   <-------		 <-------

 			- strong consistency
 			- zero replication lag
 			- slower writes

 		2. Async replication
 				w 		  w             
 			------->   ------->		 ------->
 		user		API 	   Master		Replica
 			<-------   <-------		 <-------

 			- eventual consistency
 			- some replication lag
 			- faster writes

 	II. Sharding
 		- because one node cannot handle the data/load we split it into multiple exclusive subsets writes on a particular row/document will go to particular shard
 		This way we scale overall database load

 		API -> shard1,shard2,shard3

 		note:shards are independent, no replication b/w them
 		api servers need to know whom to connect to, to get things done

 		each shard can have its own replica

 		note:some db has a proxy that takes care of routing 

--------------------------------------
4. Sharding and partitioning

Sharding: Method of distributing data across multiple machines

Partitioning: Spitting a subset of data within the same instance 

How a database is scaled ?

	A database server is just database process (mySQL, mango db) running on an ec2 machine

- You put your database in production serving real traffic
- you are getting more users that your db is unable to manage you scale up your db, give it more CPU Ram and disc  
- Your product went viral and your bulky database is unable to handle the lord so you scale up again
- but after a certed stage you know you would not be able to scale up your database because vertical scaling has a limit 
- so you will have to resort to horizontal scaling
- Say one db server was handling 1000 WPS and we cannot scale up beyond that but we are getting 1500 WPS we scale horizontally and split the data 
- by adding one more database server we reduced the load to 750 WPS on each node and thus handled higher throughput 

Each database server is does a shard and we say that the data is partitioned

 overall a database is shared while the data is partition 

 -> You partition the 100 GB of total data into 5 mutually exclusive partitions
 - each of these partitions can either live on one database server or a couple of them can share one server
 - and this depends on the number of shards you have 

How to partition the data ?

there are two categories of partitioning
1. horizontal partitioning (splitting rows in two dbs) very common
2. vertical partitioning (spliting tables in two diff dbs) monolith to microservices

 when we split the 100 GB data we could have used either of the ways but deciding which one to pick depends on load usecase and access patterns 

Advantages of sharding
- handle  large reads and writes
- increase overall storage capacity
- higher availability

disadvantages of sharding
- operationally complex
- cross-shard queries expensive

--------------------------------------
