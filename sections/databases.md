# [Databases](../README.md)

## RDS Overview

* Managed Relational Database Service
* Uses SQL as a query language
	* Postgres
	* Oracle
	* MySQL
	* MariaDB
	* Microsoft SQL Server
	* Aurora (AWS Proprietary db)
* Benefits
	* OS patching
	* Point in time restore (PITR) backups
	* Monitoring dashboards
	* Read replicas (for better read performance, up to 5)
	* Multi AZ (disaster recovery)
	* Maintenance windows
	* Scaling capability (horizontal and vertical)
* Drawback
	* Can't SSH into the instances

### Read replicas

* Up to 5
* `ASYNC` => Eventually consistent
* Can be same AZ, cross AZ or cross region
* Write to master, read from any
* __Application must update connection string to leverage read replicas__

### Multi AZ (Disaster recovery)

* `SYNC`
* Only writes/reads from master
* 1 DNS name => Automatic failover to standby
* No manual intervention in apps

### Backups

* Automatically enabled
* Automated backups
	* Daily full snapshot
	* Capture transaction logs in real-time => PITR recovery
	* 7 days backup retention => Can increased to 35 days max
* Snapshots
	* Manually triggered by user
	* Retention as long as you want

### Encryption

* Encryption at rest: AWS KMS, AES 256
* SSL certificate to encrypt data in flight
* Enforce SSL:
	* PostgreSQL: `rds.force_ssl=1` in the parameter group
	* MySQL: Within database, run: `GRANT USAGE ON *.* TO 'mysqluser'@'%' REQUIRE SSL;`
* Connect using SSL:
	* Provide the SSL trust certificate (can be downloaded from AWS)
	* Provide SSL option when connecting to database

### Security

* Deployed in private subnets
* Leverages security groups who can __communicate__
* IAM policies determines who can __manage__
* Traditional username /w password to __connect/login__
	* IAM user can now be used too for MySQL/Aurora

## RDS Multi AZ

* Failover happens
	* The primary DB instance fails
	* AZ outage
	* DB instance type is changed
	* OS of the DB instance is undergoing software patching
	* A manual failover of the DB instance (_Reboot with failover_)
* No failover for
	* long running queries
	* deadlocks
	* DB corruption errors
* Endpoint is the same after the failover
* Lower maintenance impact! Update happens on standby, if it was OK, it is promoted to master
* Backups are created on the standby -> No impact on master performance
* Multi AZ is only in the same region!

## RDS Read replica

* Helps scale read traffic
* Any read replica can be promoted as a standalone DB (manually)
* Can be cross region!
* Each read replica has it's own DNS
* Can help with disaster recovery IF using cross region read replicas
* Read replicas are not supported for Oracle
* Read replicas can be used to run BI/Analytics without affecting master performance

## Parameter groups

* Configure DB engine
* Dynamic parameters apply immediately
* Static parameters apply after reboot
* Can modify parameter group associated with DB (must reboot)
* __`rds.force_ssl=1`__ Must know for the exam

## RDS Backups vs Snapshots

* Backups
	* Continuous
	* Allows PITR
	* Happens during maintenance window 
	* When deleting a DB, you can retain the backups for a defined time period (but will be deleted eventually)
* Snapshots
	* Takes IO operations, can stop database
	* Snapshots on MultiAZ won't affect the master
	* Snapshots are incremental (after the first one, which is full)
	* Can copy & share snapshots
	* Manual snapshots never expire
	* Can create a final snapshots when deleting a DB


## RDS Security

* Encryption at rest can only be enabled when creating the DB
* Unencrypted DB -> take snapshot -> copy snapshot as encrypted -> create DB from snapshot (Same as EBS)
* Your responsibility
	* Check port / IP / security group rules for DB
	* In-database user creation and permissions
	* Creating a DB with or without Public access
	* Ensure parameter groups
* AWS responsibility
	* No SSH
	* No manual DB patching
	* No manual OS patching
	* No way to audit the underlying instance

## RDS API

* `DescribeDBInstances`
	* Get a list of DB instances you have deployed
	* Includes read replicas
	* Helps to get database version
* `CreateDBSnapshot`
	* Make a snapshot of a DB (lambda => automate backups)
* `DescribeEvents`
	* Returns events related to your DB instance
* `RebootDBInstance`
	* Forced failover by rebooting the db instance

## CloudWatch

* Default (Gathered from the hypervisor)
	* DatabaseConnections
	* SwapUsage
	* ReadIOPS / WriteIOPs
	* ReadLatency / WriteLatency
	* ReadThroughPut / WriteThroughPut
	* DiskQueueLength
	* FreeStorageSpace
* Enhanced monitoring (Agent running on instance)
	* 50 new metrics CPU/Memory/File system/Disk etc..
	* OS process list

## RDS Performance Insights

* Visualize DB performance and analyse issues that affect it
* Performance Insights Dashboard: can visualize load and filter it:
	* __By Waits__: Show which resource is the bottleneck
	* __By SQL statements__: Find the statement that is the problem
	* __By Hosts__: Find which hosts are using the DB the most
	* __By Users__: Find which user is using the DB the most
* DBLoad = the number of active sessions for the DB engine

## Aurora

* Proprietary
* Not open source BUT compatible with MySQL/PostgreSQL drivers
* Cloud optimized: 5x MySQL performance and 3x PostgreSQL performance
* Storage automatically grows in 10GB increments up to 64TB
* Up to 15 read replicas, and faster replication
* HA native, automatic failover => instantaneous
* 20% more expensive (but more efficient too)
* Backtrack (PITR without backups)

### High availability

* 6 copies of your data across 3 AZs
	* 4 copies required to Write
	* 3 copies required to Read
* Self healing with P2P replication
* Storage is stripped across 100s of volumes
* 1 master (takes read and writes)
	* Failover takes less than 30 seconds for master
* Supports cross region replication

### Aurora DB Cluster

* __Writer endpoint__
	* Always points to master
	* In case of failover, the endpoint won't change
* __Reader endpoint__
	* Connection LoadBalancing
	* Connects to all Read replicas automatically

### Aurora Serverless

* Don't need to chose instance size
* Only supports MySQL 5.6, PostgreSQL
* Can migrate between Aurora Cluster <-> Aurora Serverless
* Aurora Capacity Units (ACU)
* Billed in 5 minutes increments ACU

## ElastiCache

* Managed Redis or Memcached
* Caches are in-memory databases with really high performance and low latency
* Helps reduce load off for DBs for read intensive workloads
* Helps make your application stateless
* Write Scaling: using __sharding__
* Read Scaling: using __read replicas__
* Multi AZ with failover
* AWS takes care of OS maintenance/patching, optimizations, setup, configuration, monitoring, backups

### Redis

* In memory Key-value store
* Super low latency
* Cache survives reboot by default (persistent)
* Great for
	* User sessions
	* Leaderboard
	* Distributed states
	* Relieve pressure from RDS database
	* Pub / Sub capability for messaging
* Multi AZ with auto failover
* Read replicas

### Memcached

* In memory object store
* Cache DOESN'T survive reboot

## [Back](../README.md)