# [EC2](../README.md)

## Change instance type:

* Stop instance
* Change instance type
* EBS backed -> Won't lose data

## Placement groups:

* Cluster: 
	* Single AZ low latency
	* Same hardware/rack
	* 10 GBps
	* No T2 types
* Spread:
	* Multi AZ
	* Maximum 7 instances in any AZ
	* Different HW
* Partition:
	* 7 partitions per AZ
	* up to 100 instances
	* Big data distributed applications (HDFS, Kafka )

## Shutdown behaviour (not same as instance protection)

* Stop (default) or Terminate
* Instance settings -> Change shutdown behaviour
* Through the OS (`sudo shutdown now`)
* > Instance protection activated && shutdown is terminate: Shutdown from the OS will RESULT IN TERMINATION of the instance

## Launch troubleshooting

* `#InstanceLimitExceeded`
	* maximum number of instances in the region is reached (default: 20)
	* Resolution:
		* ticket to increase
		* use another region
* `#InsufficientInstanceCapacity`
	* AWS ran out of on-demand instances on the __AZ__!
	* Resolution:
		* Wait
		* 1 at a time
		* Change regions
* Instance terminates Instantly (Pending to terminated)
	* Reached EBS volume limit
	* Corrupted EBS volume
	* EBS encrypted and no access to KMS
	* instance store missing a part
	* Debug:
		* State transition reason
		* State transition message

## SSH troubleshooting

* `Unprotected key file`: pem has 400 permissions
* `Host key not found`: wrong username
* `Connection timeout`: SG or CPU 

## Instance launch types

* On-demand
	* short workload, predictable pricing
* Reserved
	* long workloads (1 or 3 years)
* Convertible
	* Reserved, but can change instance type
* Scheduled
	* short window reserved
* Spot
	* Bidding
	* cheap, can loose instances
* Dedicated instance
	* Not sharing hardware
	* can share from the same account
* Dedicated host
	* entire server
	* control placement
	* 3 years

## Instance types [compare](ec2instances.info)

* R
	* RAM - memory heavy
* C
	* Compute/Databases
* M
	* medium: between R and C (general, webapp)
* I
	* good I/O, databases
* G
	* GPU: Video rendering, machine learning
* T2/T3 Burstable
* T2/T3 Unlimited

## AMI

* Region specific! (__AMI ID will be different in each region__)
* Stored in S3
* Use public AMIs / Rent from third parties
* Private and region locked by default
* Copy (directly)
	* Without "Create volume" permissions 
		* Can't copy encrypted AMI
		* Can't copy AMI with billingProduct
		* Workarounds: Launch instance from AMI then create new from it

## Elastic IP

* Mask failure of an instance or se by quickly remapping the address
* 5 IP by default
* Associate -> Disassociate -> Associate

## CloudWatch monitoring

* AWS provided
	* CPU, Network , Disk, Status
	* (Free) Default: 5 minutes
	* (Paid) Detailed: 1 minutes
* Custom (your responsibility):
	* 1 minute, can be more frequent up to 1 sec
	* RAM, application level metrics
	* Needs IAM role to push to CW
* CPU: Utilization + Credits if T? instance
* Network: In and out traffic
* Status:
	* Instance: EC2 VM check
	* System: Hardware check
* Disk (only for instance store)
* Custom:
	* CloudWatch agent or scripts
	* Needs CW IAM permissions

## CloudWatch logs

* Default: no logs
* Needs CW agent

## [Back](../README.md)