# [Additional](../README.md)

## AutoScaling

* `Amazon EC2`
	* Launch or terminate Amazon EC2 instances in an Amazon EC2 Auto Scaling group.
* `Amazon EC2 Spot Fleets`
	* Launch or terminate instances from an Amazon EC2 Spot Fleet
	* Automatically replace instances that get interrupted for price or capacity reasons
* `Amazon ECS`
	* Adjust ECS service desired count up or down to respond to load variations.
* `Amazon DynamoDB`
	* Enable a DynamoDB table or a global secondary index to increase its provisioned read and write capacity to handle sudden increases in traffic without
* `Amazon Aurora`
	* Dynamically adjust the number of Aurora Read Replicas provisioned for an Aurora DB cluster to handle sudden increases in active connections or workload.

## Backup

* Backup and restore
* Pilot light
* Warm standby
* Multisite

## Kinesis

* Kinesis Data Streams (KDS) is a massively scalable and durable real-time data streaming service
* Data collected is available in milliseconds to enable real-time analytics
	* Real-time dashboards
	* Real-time anomaly detection
	* Dynamic pricing

## Redshift

* Petabyte storage service for OLAP applications.
* Enhanced VPC Routing
	* Forces all `COPY` and `UNLOAD` traffic between your cluster and your data repositories through your VPC
* Spectrum
	* Primarily used to run queries against exabytes of unstructured data in Amazon S3
* Automated backups
	* Can configure to automatically copy snapshots (automated or manual) for a cluster to another region

## Step functions

* Provides serverless orchestration for modern applications
* Centrally manages a workflow by breaking it into multiple steps, adding flow logic, and tracking the inputs and outputs between the steps

## SQS

* `ApproximateNumberOfMessages`: Returns the approximate number of messages available for retrieval from the queue.

## [Back](../README.md)