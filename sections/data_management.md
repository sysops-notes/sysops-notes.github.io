# [Data and storage management](../README.md)

## Athena

* Serverless service to perform __analytics directly against S3 files__ (built on Presto)
* SQL language to query the files
* Has JDBC / ODBC driver
* Charged per query and amount of data scanned
* Use cases:
	* Analyze VPC FlowLogs, ELB logs, CloudTrail logs, Business Intelligence

## CloudFront

* Content Delivery Network (CDN)
* Improves performance by caching content at the edge _(~136 edge points)_
* Popular with S3 but works well with EC2 & LB as well
* Can help protect against network attacks
* Can provide SSL encryption using ACM at edge
* Can also force CloudFront to talk to the application via HTTPS
* Supports RTMP
* Origin Access Identity (CloudFront user, using bucket policy we can restrict for this user only)

### CloudFront monitoring

* Can log every request made to CloudFront into an S3 bucket
* Can get reports based on access logs, but don't have to enable access logs to get these reports
	* `Cache statistics`: Get statistics on viewer requests grouped by HTTP status code
	* `Popular objects report`: Determine what objects are frequently being accessed
	* `Top referrers`: Display a list of the 25 website domains that originated the most HTTP and HTTPS requests for objects
	* `Usage reports`: Know the number of HTTP/HTTPS requests that CloudFront responds to from edge locations
	* `Viewers report`: Determine the locations of the viewers that access your content and the types of browsers they use
* CF caches `HTTP 4xx` (user doesn't have access) and `5xx` (gateway issues) status codes returned by S3

## Snowball

* Physical data transport that helps move data in or out AWS __(TBs or PBs)__
* Up to `80 TB`
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
	* Storage optimized (24 vCPU) - `100TB`
	* Compute optimized (52 vCPU & optional GPU) - `42 TB`
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

## [Back](../README.md)