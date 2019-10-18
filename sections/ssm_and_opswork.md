# [SSM & OpsWork](../README.md)

Managing EC2 at scale

## Systems Manager (SSM) - general

* Manage EC2 and on-premise
* Operational insights
* Detect problems
* Patch
* Windows & Linux
* Free
* Requires SSM agent to be installed
* Requires IAM permissions 

## SSM ()

* __Regional__ Resource groups (based on tags) -> Easy patching
* `Documents`: yaml or json, common denominator between SSM features
* `Run command`:
	* Executes a document or just a run command
* Patch:
	* Inventory                          => List software on an instance
	* Inventory + Run command            => Patch software
	* Patch manager + maintenance window => Patch OS
	* Patch manager                      => Gives compliance
	* State manager                      => Ensures instances are in a compliant state
* Session manager

## Lost SSH key to EC2

* EBS Backed
	* Method 1: Manual
		* Stop instance
		* Detach root volume
		* Attach it to a new instance as a data volume
		* Change `~/.ssh/authorized_keys`
		* Detach / Move back to original instance
	* Method 2: SSM
		* `AWSSupport-ResetAccess`
* Instance store backed
	* Nothing to do, terminate instance
	* _(Could use SSM)_

## Parameter Store

* Secure storage
* Seamless encryption (KMS)
* Serverless, scalable, durable, free, has SDK
* Version tracked
* IAM controlled
* Notification through CW Events (modified/deleted etc)
* `GetParameters` and `GetParametersByPath`
* `--with-encryption`

## OpsWorks

* Managed Chef and Puppet
* Anything to do with Chef or Puppet -> Use OpsWork in the exam

## [Back](../README.md)