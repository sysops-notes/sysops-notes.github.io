# [Elastic Beanstalk](../README.md)

## General

* Architecture models
	* `Single instance`: Great for dev
	* `HA with LB`: LB + ASG (prod web apps)
	* `HA without LB`: ASG only (prod no-web apps)
* Components
	* Application
	* Application versions
	* Environment name

## Deployment modes

* `All at once`: Fastest, but has downtime
	* Code deployed: existing instances
	* Rollback process: manual redeploy
	* Deploy time: fastest
	* Downtime: Yes
	* DNS change: No
* `Rolling`: Few instances (_buckets_) at a time, then next bucket once the previous is healthy
	* Code deployed: existing instances
	* Rollback process: manual redeploy
	* Downtime: No
	* DNS change: No
* `Rolling with additional batches`: Rolling but extra instances (Does have additional costs)
	* Code deployed: new and existing instances
	* Rollback process: manual redeploy
	* Downtime: No
	* DNS change: No
* `Immutable`: New ASG + everything, then change, finally destroy old (High costs)
	* Code deployed: new instances
	* Rollback process: Terminate new instances
	* Downtime: No
	* DNS change: No
	* Temp ASG
	* 1 new instance in temp ASG -> if healthy, add remaining
	* Merge temp asg with previous
	* Terminate old version in merged ASG
* `Blue/Green deployment`: Not a "direct feature" of EB
	* Code deployed: new instances
	* Rollback process: Swap URL
	* Downtime: No
	* DNS change: Yes
	* Zero downtime

## For the exam

* Log can be put into CloudWatch logs
* AWS manages underlying infrastructure 
* Can put Route53 Alias or CNAME on top of EB URL
* Runtime patches are AWS's responsibility
* Rolling:
	* EC2 has a base AMI (can configure)
	* EC2 gets new code
	* EC2 resolves new dependencies (can take a lot of time)
	* Apps are swapped
	* Question: How to make it fast?
		* Use a golden AMI

### Troubleshooting

* Env turns to Red
	* Review environment events
	* Pull logs
	* Rollback previous version
* Make sure SG are correctly configured if accessing external resources
* Increase deployment timeout

## [Back](../README.md)