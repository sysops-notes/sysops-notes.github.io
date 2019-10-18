# [S3: Data and storage management](../README.md)

## Versioning

* Can protect against hackers, cause each version is a separate file
* Cannot delete bucket unless all objects AND all versions are deleted first

## MFA Delete

* First need to enable versioning
* Only the bucket owner (root account) can enable/disable MFA-Delete
* Can only enable is via the CLI (as of now)
* Need to MFA before
	* permanently deleting an object version
	* suspend versioning
* DON'T need MFA for
	* enabling versioning
	* listing deleted versions
* If enabled, you can't do any permanent delete as the UI doesn't use MFA -> must do all permanent delete in the CLI

## Default encryption Vs Bucket policies

* Old way: Bucket policy
* New way: Just enable default encryption on Bucket
* NOTE: Bucket policy evaluated before default encryption

## Access logs

* Any request will be logged (Can be from another account, access denied etc..)
* Can use Data analysis tools (Amazon Athena)
* Can use prefix in logging bucket
* Can take some time for the logs to show up

## Cross Region Replication

* Asynchronously replicate
* Must enable source and destination bucket versioning!
* Buckets can be in different accounts
* Must give proper IAM permissions

## Pre-signed URL

* Can be generated via SDKs or CLI
* Default: valid for 1 hour (3600 seconds) `--expires-in [time_in_seconds]`
* The URL will inherit your permissions], so whoever uses the link will be able what you could
* `aws configure set default.s3.signature_version s3v4`
* Set `--region` appropriately

## CloudFront overview

* Content Delivery Network (CDN)
* Improves performance by caching content at the edge (~136 edge points)
* Popular with S3 but works well with EC2 & LB as well
* Can help protect against network attacks
* Can provide SSL encryption using ACM at edge
* Can also force CloudFront to talk to the application via HTTPS
* Supports RTMP
* Origin Access Identity (CloudFront user, using bucket policy we can restrict for this user only)

## CloudFront monitoring

* Can log every request made to CloudFront into an S3 bucket
* Can get reports based on access logs, but don't have to enable access logs to get these reports
	* cache statistics
	* popular objects report
	* top referrers
	* usage reports
	* viewers report
* CF caches `HTTP 4xx` (user doesn't have access) and `5xx` (gateway issues) status codes returned by S3

## S3 Inventory

* Helps to manage storage
* __Audit and report on replication and encryption status of your objects__
* Data for the Source bucket has to go to a Target bucket (need to set up IAM permissions)

## S3 Storage tiers

* `Standard`: General purpose
	* 99,99% Availability
	* 11 9 durability (99,999999999%) across multiple AZ
	* Can sustain 2 az outage 
* `Reduced redundancy storage`: DEPRECATED
	* 99,99% Durability
* `Infrequent Access (IA)`: 
	* Not accessed regularly but when it does, need quick access
	* 99,99% Availability
	* 11 9 durability (99,999999999%) across multiple AZ
	* Low cost compared to Standard
	* Fee when accessing objects
* `One Zone Infrequent Access (One zone IA)`: 
	* Same as IA but only in 1 AZ
	* If AZ is DESTROYED (not down), then data is lost
	* 99,95% Availability 
	* Lower cost than IA (20% )
* `Intelligent Tiering`: announced in 2018
	* Best of all, as it can move object to the correct tier
	* Small monthly fee for auto-tiering
	* Only 99.9% Availability though
* `Glacier`:

## S3 Lifecycle rules

* Set of rules to __move data between different tiers__ to save costs
	* Transition actions
		* Move objects into another tier
	* Expiration actions
		* Delete objects after a certain period
	* Delete incomplete multipart uploads

## S3 Storage class Analytics

* Setup analytics to __help determine when to transition objects to different tiers__
* Does NOT work for OneZone IA or Glacier
* Daily report
* First report takes 1-2 days

## Glacier overview

* Low cost storage for archiving / backup
* Data is retained for a long time (10s of years)
* Alternative to on-premise magnetic tape storage
* 11 nine durability
* $0.004/GB + retrieval fee
* `Object` in other tiers = `Archive` in Glacier
* An archive can be up to 40 TB
* Archives are stored in `Vaults` (again, different naming convention)
* Operations
	* `Upload` (single, or multi part upload)
	* `Download`: Initiate a retrieval request, wait for file to be ready, limited time to download!
	* `Delete` (Use SDK or Glacier REST API)
* Restore links have an expiry date
* 3 retrieval options:
	* __Expedited__: 1 to 5 minutes retrieval, `$0.03/GB` + `$0.01/request`, Capacity units
	* __Standard__: 3 to 5 hours retrieval, `$0.01/GB` + `$0.05/ 1000 request`
	* __Bulk__: 5 to 12 hours retrieval, `$0.025/GB` + `$0.025/ 1000 request`

### Vault lock & policies

* Vault Collection of archives
* Each Vault has:
	* 1 Vault __access policy__ (JSON), similar to bucket policies
	* 1 Vault __lock policy__
		* Immutable
		* A policy that has been locked. E.g.: Can never be changed, removed
			* Example 1: `WORM policy`: Write Once, Read Many: 
			* Example 2: Cannot delete archives that are younger than 1 year
		* When creating a lock policy, you get a LockID: 24 hours to confirm lock policy

## Snowball

* Physical data transport that helps move data in or out AWS __(TBs or PBs)__
* Alternative to moving data over network (save network fee)
* Secure, temper resistant, KMS 256 bit encryption
* Tracking using SNS and text messages
* E-ink shipping label
* Pay per data transfer job
* "If it takes more than 2 weeks over network, choose snowball"
* Process
	* Request snowball device from Amazon
	* Install snowball client on your servers
	* Copy files over
	* Ship back device (automatically goes to the correct facility)
	* Data loaded into an S3 bucket
	* Snowball is wiped

### Snowball Edge

* Adds computational capability to the device
* __100 TB with either__
	* Storage optimized (24 vCPU)
	* Compute optimized (52 vCPU & optional GPU)
* Supports custom AMI, so you can perform operations on the go
* Support custom Lambda function

### Snowmobile

* An actual truck
* Exabytes of data (1 EB = 1 million TB)
* 1 truck = 100 PB -> Multiple trucks in parallel
* Better than snowball if you want to transfer more than 10 PB

## Storage gateway

* Exposes S3 data to on-premise
* AWS storage:
	* Block (EBS, Instance store)
	* File (EFS)
	* Object (S3)
* Types:
	* __File gateway__: View files locally on-premise but they are backed in S3
		* Configured S3 buckets are accessible using __NFS__ or __SMB__ protocols
		* Support for all type of S3
		* Bucket access using IAM roles for EACH File gateway
		* Most recently used data is cached in gateway
		* Can be mounted on many servers
	* __Volume gateway__: View volumes locally on-premise but they are backed in EBS
		* __iSCSI__ Protocol backed by S3
		* Backed by EBS snapshots
		* Cached volumes: Low latency access to most recent data
		* Stored volumes: Entire dataset is on-premise, scheduled backups to S3
	* __Tape gateway__: View archives locally on-premise but they are backed in Glacier
		* Backups
		* Virtual Tape Library (VTL)
		* Backed by Glacier
		* Summary
* General question
	* On premise data to Cloud => Storage gateway
* Detailed question
	* File access / NFS => File gateway
	* Volume / block storage / EBS / iSCSI => Volume gateway
	* VTL Tape Solution / Backup with iSCSI => Tape gateway

## Athena

* Serverless service to perform __analytics directly against S3 files__ (built on Presto)
* SQL language to query the files
* Has JDBC / ODBC driver
* Charged per query and amount of data scanned
* Use cases:
	* Analyze VPC FlowLogs, ELB logs, CloudTrail logs, Business Intelligence

## [Back](../README.md)