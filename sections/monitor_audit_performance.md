# [Monitoring, Audit & Performance](../README.md)

## CloudWatch

### Metrics

* Metrics for all AWS services
* Metric is a variable to monitor
* Metrics belong to __namespaces__ (groups)
* __Dimension__ is an attribute of a metric (instance_id, env, etc...)
* Up to 10 dimensions
* Metrics have timestamps
* Can create a dashboard out of metrics

#### EC2 Detailed monitoring

* EC2 instance have metrics every 5 minutes (default)
* Detailed monitoring can be every 1 minute (extra cost)
* AWS free tier allows for 10 detailed monitoring metrics

#### Custom metrics

* Possible to define your own metrics
* Able to use dimensions
* Metric resolution is 1 minute _(default)_, can go up tp every second _(extra fee)_
* API: `PutMetricData`
* Throttle errors -> Exponential back off

### Dashboard

* Quick access for key metrics
* Dashboards are __global__
* Can include graphs from different regions
* Can change time zone and time range for dashboards
* Can set up auto refresh
* Pricing
	* 3 dashboards (up to 50 metrics): Free
	* $3/month/dashboard 
* How to create global dashboard -> Add metrics from different region
* __Cannot aggregate metrics from regional metrics!__

### Logs

* Application can send logs using CLI
* AWS Services that are logging directly:
	* ElasticBeanstalk
	* ECS
	* Lambda
	* VPC Flow logs
	* API Gateway
	* CloudTrail based on filter
	* CloudWatch Log Agent
	* Route53: DNS queries
* CW Logs can be exported to S3 or ElasticSearch
* Architecture
	* `Log group`
	* `Log streams`
	* `Expire policy`
* Make sure IAMs are correct
* Logs can be encrypted using KMS at group level
* Can use filter expressions
	* Search for a specific IP
	* Set up metric filter to trigger alarms
* Log insights
	* Common queries to Dashboard

### Alarms

* Trigger notification on any metric
* Alarms can go to Auto Scaling, EC2 Actions, SNS notifications
* Various options (sampling, %, min, max, etc...)
* Alarm states
	* `OK`
	* `INSUFFICIENT_DATA`
	* `ALARM`
* Period
	* Length of time to evaluate metric
	* High resolution custom metrics: only 10 or 30 seconds
* Targets
	* Stop, Terminate, Reboot, Recover EC2 instances
	* Trigger autoscaling actions
	* Send message to SNS (from which you can do pretty much anything)
* CW doesn't test or validate the action assigned
* To test an alarm use the CLI
	* `aws cloudwatch set-alarm-state --alarm name "alarm" --state-value ALARM --state-reason "Testing"`

### Events

* Source + Rule => Target
* Schedule: Cron jobs
* Event pattern: Event rules to react to a service doing something
* Triggers to Lambda functions/SQS/SNS/Kinesis ...
* Creates a small JSON document to give further information about the change

## CloudTrail

* Provides governance, compliance and audit for your AWS account
* (Track every API call made to the account)
* Can put to CW Logs
* If a resource is deleted, look in CloudTrail _(to see who did that)_
* Stores for 90 days (then you need to store them somewhere else if you want to keep them)
* Default UI only shows `Create` `Modify` and `Delete` events
* __CloudTrail has an option to validate file integrity__
	* E.g. Can detect if the log file has been change after it has been written to S3
* Can create Trails
	* Detailed list of every event you select
	* Can store them in S3
	* If trails are put to S3, they have SSE-S3 encryption by default (can choose KMS)
	* Control via IAMs or Bucket policies
	* Can be region specific or global

## Config

* Helps with auditing and compliance of your AWS resources
* Records configuration and changes over time
* Records compliance over time
* Can store AWS Config data in S3 (and use Athena)
* Questions can be solved:
	* Is there unrestricted SSH access to my SGs
	* Is there any S3 buckets with public access
	* How my ALB configuration changed
	* etc...
* Can receive alerts _(SNS notification)_ on changes
* Regional service
* Can be aggregated across multiple accounts/regions

### Config rules

* 80+ AWS managed config rules
* Can create custom rules _(must be defined in AWS Lambda)_
	* Eg.: Evaluate if all EBS disk is gp2 type
* Rules can be evaluated
	* For each config change
	* Or regular time intervals
* Pricing
	* No free tier
	* $2/active rule/region/month (less after 10 rules)
* Can integrate with CloudTrail to see who made the changes
 
## [Back](../README.md)