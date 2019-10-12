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
* `Rolling`: Few instances _(buckets)_ at a time, then next bucket once the previous is healthy
* `Rolling with additional batches`: Rolling but extra instances (Does have additional costs)
* `Immutable`: New ASG + everything, then change, finally destroy old (High costs)
	* Temp ASG
	* 1 new instance in temp ASG -> if healthy, add remaining
	* Merge temp asg with previous
	* Terminate old version in merged ASG
* `Blue/Green deployment`: Not a "direct feature" of EB

|                               |       Code deployed      |         Rollback        | Downtime | DNS Change |
|:-----------------------------:|:------------------------:|:-----------------------:|:--------:|:----------:|
|          All-at-once          |    Existing instances    |     Manual redeploy     |    Yes   |     No     |
|            Rolling            |    Existing instances    |     Manual redeploy     |    No    |     No     |
| Rolling with additional batch | Existing & New instances |     Manual redeploy     |    No    |     No     |
|           Immutable           |       New instances      | Terminate new instances |    No    |     No     |
|          Blue/Green*          |       New instances      |         Swap URL        |    No    |     Yes    |

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