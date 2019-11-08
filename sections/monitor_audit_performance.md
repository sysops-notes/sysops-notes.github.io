# [Monitoring, Audit & Performance](../README.md)

## CloudWatch

### Metrics

* Metrics for all AWS services
* Metric is a variable to monitor
* Metrics belong to __namespaces__ (groups)
* __Namespace is mandatory!__
* __Dimension__ is an attribute of a metric (instance_id, env, etc...)
* Up to 10 dimensions
* Metrics have timestamps
* Metrics exist only in the region in which they are created
* Metrics cannot be deleted, but they automatically expire in 14 days if no new data is published to them
* Retention
	* One minute data points are available for 15 days.
	* Five minute data points are available for 63 days.
	* One hour data points are available for 455 days (15 months).
* Can create a dashboard out of metrics
* Can aggregate metrics

#### EC2 Detailed monitoring

* EC2 instance have metrics every 5 minutes (default)
* Detailed monitoring can be every 1 minute (extra cost)
* AWS free tier allows for 10 detailed monitoring metrics

#### Custom metrics

* Possible to define your own metrics
* Able to use dimensions
* Metric resolution is 1 minute _(default)_, can go up tp every second _(extra fee)_
* API: `PutMetricData`
	* Can only publish one data point per call
* __CloudWatch aggregates the data to a minimum granularity of 1 minute__
* Time stamp can be up to __2 weeks in the past__ and up to __2 hours into the future__
* Throttle errors -> Exponential back off
* New metrics can take 15 minutes to show up in the console

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
* By default it stores the log data indefinitely
* Retention can be changed for each log group at any time
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
* Alarms exist only in the region in which they are created.
* Alarm actions must reside in the same region as the alarm
* Alarm history is available for the last 14 days.
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
* Default UI only shows `Create`, `Modify` and `Delete` events
* __CloudTrail has an option to validate file integrity__
	* E.g. Can detect if the log file has been changed after it has been written to S3
* A trail can be applied to all regions or a single region
* A single SNS topic for notifications and CloudWatch Logs log group for events would suffice for all regions
* Can create Trails
	* Detailed list of every event you select
	* Can store them in S3
	* If trails are put to S3, they have SSE-S3 encryption by default (can choose KMS)
	* Control via IAMs or Bucket policies
	* Can be region specific or global
* CloudTrail supports five trails per region
* A trail that applies to all regions counts as one trail in every region
* __For global services__ such (`IAM`, `AWS STS`, `CloudFront`) events are delivered to __any trail__ that has the __Include global services option enabled__
* `AWS OpsWorks` and `Route53` actions are logged in the __US East (N. Virginia) region__

## Config

* Helps with auditing and compliance of your AWS resources
* Records configuration and changes over time
* Records compliance over time
* Can store AWS Config data in S3 (and use Athena)
* Questions can be solved:
	* Is there unrestricted SSH access to my SGs
	* Is there any S3 buckets with public access
	* How my ALB configuration changed
* Use cases
	* Security Analysis & Resource Administration
	* Auditing & Compliance
	* Change Management
	* Troubleshooting
	* Discovery
* Can receive alerts _(SNS notification)_ on changes
* Regional service
* AWS Config creates configuration items for every supported resource in the region
* Can be aggregated across multiple accounts/regions
* In cases where several configuration changes are made to a resource in quick succession (within a span of few minutes), It will only record the latest configuration of that resource
* AWS Config does not cover all the AWS services
	* The configuration management process can be automated using API + code

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