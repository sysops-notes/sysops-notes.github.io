# [EBS & EFS](../README.md)

## Introduction

### EBS Volume

* Network drive to attach to a running instance and persist data on it
* Can detach from an instance and attach to another __in the same AZ!__
* Can move between AZs but need to snapshot first
* Have a provisioned capacity (size GBs, IOPS)
	* Billed on this
* Can increase capacity over time
* Types
	* __GP2: General Purpose SSD__: Balanced
	* __IO1: Provisioned IOPS SSD__: Highest performance SSD, low latency, high throughput workloads, Critical Database
	* __SP1: Throughput Optimized HDD__: Low cost HDD, frequently accessed throughput intensive workloads, Streaming
	* __SC1: Cold HDD__: Lowest cost HDD, less frequently accessed workloads
* Boot volume can only by _GP2_ or _IO1_
* Some commands:
	* `lsblk`: List volumes
	* `df -h`: See partitions
	* `vim /etc/fstab`: Edit mount volumes on boot

## GP2 Volume burst

* Volume is less than 1000GB (means IOPS is less than 3000) then it can burst to 3000 IOPS
* If balance is 0 all the time
	* Increase volume size
	* Switch to IO1
* Monitor via CloudWatch the I/O credit

## Computing MB/s based on IOPS

* GP2
	* Throughput = (Volume size in GiB) x (IOPS per Gi B) x (I/O size in KiB)
	* Max 250  MiB/s
* IO1
	* Throughput = (Provisioned IOPS) x (I/O size in KiB)
	* Max 500 MiB/s (32000 IOPS) | Max 1000 MiB/s (64000 IOPS)

## EBS Volume resize

* Can only increase
	* Volume size
	* IOPS _(only in IO1)_
* After resize, need to repartition the drive
* Can do it while its being used

## EBS Snapshots

* __Incremental__: only backs up changed blocks
* Backups use the disk's I/O (don't run backup when handling workload)
* Stored in S3 (not seen by you)
* Max 100000 backups per Account
* Can copy snapshots between AZs / Regions
* Can create AMI from snapshot
* EBS volumes restored from snapshots need to be pre-warmed (`fio` pr `dd`)
* Snapshots can be automated via Amazon Data Lifecycle Manager

## EBS Migration

* Snapshot volume
* _(Optional) Copy to another region_
* Create a volume from the snapshot in the AZ of your choice

## EBS Encryption

* All encryption is handled by Amazon _(KMS: AES-256)_
* Impact is minimal
* Data at rest is encrypted
* Data in flight is encrypted
* All snapshots are encrypted
* All volumes are encrypted
* Encrypt an un-encrypted volume
	* Create snapshot
	* Encrypt snapshot
	* Create new volume from encrypted snapshot
	* Attach new volume in place of the old

## Instance Store

* Instance Store
	* Physically attached to the instance
	* Ephemeral storage
	* Pros
		* Better I/O
		* Goof for buffer, cache, scratch data, temporary content
		* Data survives reboot
	* Cons
		* On stop/termination data is lost
		* Can't resize volume
		* Backups must be operated by the user

## EBS For SysOps Exam

* If you want to use the root volume after termination -> Set `Delete on Termination` to `No`
* If you use EBS for high-performance: Use EBS optimized instance types
* EBS volume is unused -> Still paying for that
* High wait times / Slow response from SSD -> increase IOPS 
* EC2 won't start with EBS set as root volume (Don't name it `/dev/xvda`)
* After increasing volume size, still need to repartition it

## EBS RAID

* EBS is already redundant (Replicated withing AZ)
* RAID is possible as long as the OS supports it (have to do it through OS, not an Amazon "feature")
	* `RAID 0`
		* __Increase performance__
		* If 1 disk fails, you lose data
		* Could go to 10K IOPS -> 10 disk each having 1k IOPS
		* Use cases
			* App need a lot of IOPS and doesn't need fault tolerance
			* DB that has replication built-in
	* `RAID 1`
		* __Increase fault tolerance by Mirroring__
		* Have to send data to 2 EBS -> 2x Network
		* Use cases
			* App that needs to increase volume fault tolerance 
	* RAID 5 - Not recommended for EBS 
	* RAID 6 - Not recommended for EBS 

## EBS CloudWatch

* Metrics
	* `VolumeIdleTime`: number of seconds when no read/write is submitted
	* `VolumeQueueLength`: number of operations waiting to be executed. (High number means probably IOPS or application issue)
	* `BurstBalance`: If becomes 0, need a volume with more IOPS
* GP2: every 5 minutes
* IO1: every 1 minutes
* EBS Volume have status checks
	* `Ok`: Performing good
	* `Warning`: Performance below expected
	* `Impaired`: Stalled, performance is severely degraded
	* `Insufficient-data`: Metric data collection is in progress

## EFS

* Managed NFS that can be mounted on many EC2 instance
* Works multi AZ
* Highly available/scalable
* Expensive (~3 times GP2), pay per use
* Need to attach SG to EFS in order to use
* Can be used simultaneously from all AZs
* Uses NFSv4.1
* Compatible with Linux based AMIs (No Windows yet)
* Performance mode
	* General (default)
	* Max performance (when used by 1000s of instances)
* Can sync files from on-premise
* Can backup to another EFS (incremental, can choose frequency)
* Encryption at rest using KMS

## [Back](../README.md)