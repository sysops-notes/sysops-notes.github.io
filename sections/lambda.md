# [Lambda](../README.md)

## General

* AWS Lambda offers Serverless computing
	* Lets you run code without provisioning or managing servers
	* Priced on a pay per use basis and there are no charges when the code is not running
	* Zero administration
	* Does not provide access to the underlying compute infrastructure
* Performs all the operational and administrative activities on your behalf
	* Capacity provisioning
	* Monitoring fleet health
	* Applying security patches to the underlying compute resources
	* Deploying code
	* Running a web service front end
	* Monitoring and logging the code
* Core components
	* `Event source`: AWS service or custom application that publishes events
	* `Lambda function`: The custom code that processes the events
* All calls made to AWS Lambda must complete execution within `900 seconds`
* Default timeout is `3 seconds`
	* Timeout can be set the timeout to any value between `1` and `900` seconds
* `AWS Step Functions` can help coordinate a series of Lambda functions in a specific order
	* Multiple Lambda functions can be invoked sequentially, passing the output of one to the other and/or in parallel, while the state is being maintain by Step Functions
* `AWS X-Ray` helps tracing for Lambda functions

## Scalability and availability

* Provides easy scaling and high availability to the code
* Designed to process events within milliseconds.
	* Latency will be higher immediately after a Lambda function is created, updated, or if it has not been used recently.
* Designed to
	* Run many instances of the functions in parallel
	* Use replication and redundancy to provide high availability 
* There are no maintenance windows or scheduled downtimes
* Has a default safety throttle for number of concurrent executions per account per region

## Security

* Stores code in S3 and encrypts it at rest and performs additional integrity checks while the code is in use
* Each Function runs in its own isolated environment, with its own resources and file system view

## Lambda function

* Each Lambda function has associated configuration information, such as its name, description, entry point, and resource requirements
* Each Lambda function receives `500MB of non-persistent disk space` in its own /tmp directory.
* Design Lambda function as stateless
	* State can be maintained externally in DynamoDB or S3
* Lambda function can be granted permissions to access other resources using an IAM role
* Automatically monitors functions, reporting real-time metrics through `CloudWatch`
	* Total requests
	* Latency
	* Error rates
	* Throttled requests
* Automatically integrates with `CloudWatch logs`, creating a log group for each function

### Versioning

* Each function has a single, current version of the code
* Each function version has a unique ARN and after it is published it is immutable
* Versioning can be implemented using `Aliases`
	* Lambda supports creating aliases, which are mutable, for each Lambda function versions
	* Alias is a pointer to a specific function version, with unique ARN
	* Each alias maintains an ARN for a function version to which it points
	* An alias can only point to a function version, not to another alias
	* Alias helps in rolling out new changes or rolling back to old versions

## [Back](../README.md)