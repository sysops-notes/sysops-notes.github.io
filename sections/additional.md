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

## CodeDeploy

* The deployment group contains settings and configurations used during the deployment

## Backup

* Backup and restore
* Pilot light
* Warm standby
* Multisite

## Elastic MapReduce (EMR)

* Amazon EMR is the industry leading cloud-native big data platform, allowing teams to process vast amounts of data quickly, and cost-effectively at scale

## Limits Monitor

* Automatically provisions the services necessary to proactively track resource usage and send notifications as you approach limits

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
* Amazon Redshift logs information in the following log files
	* `Connection log`: Logs authentication attempts, and connections and disconnections.
	* `User log`: Logs information about changes to database user definitions.
	* `User activity log`: Logs each query before it is run on the database.

## Step functions

* Provides serverless orchestration for modern applications
* Centrally manages a workflow by breaking it into multiple steps, adding flow logic, and tracking the inputs and outputs between the steps

## SQS

* `ApproximateNumberOfMessages`: Returns the approximate number of messages available for retrieval from the queue.
* Messages that can't be processed (consumed) successfully can be forwarded for `Dead-letter queues`

## [Back](../README.md)