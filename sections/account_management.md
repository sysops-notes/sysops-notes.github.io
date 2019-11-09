# [Account management](../README.md)

## AWS Status Health Dashboard

* Shows all regions and all service's health
* Shows historical information for each day
* Has an RSS feed that you can subscribe to

### Personal Health dashboard

* Global service
* Shows how AWS outages directly impact you
* Shows impact on your resources
* List issues and actions to remediate

## AWS Organizations

* Global service
* Allows to manage multiple accounts
* Main account -> Master account (can't change)
* Member accounts can only be part of one organization
* Consolidated billing accounts -> single payment method
* Pricing benefits from aggregated usage
* API is available to automate AWS account creation

### OUs and Service Control Policies

* Organize accounts in: __Organizational Units (OU)__
	* eg. dev, test, prod, finance....
* Can nest OUs in OUs
* Apply SCPs in OUs
	* Permit/Deny access to AWS services
	* Similar syntax to IAMs
	* It's a filter to IAMs
* Helpful for
	* Sandbox account creation
	* Separate dev and prod
	* Only allow approved services

## Service catalog

* __Self-service portal__ to launch a set of __authorized products__ pre-defined by __admins__
* Admin tasks
	* Create product - CloudFormation templates (database, instance etc)
	* Create portfolio (collection of products)
	* IAM permissions to access portfolios
* User tasks
	* Launch product + parameterize

## Billing alarms

* Before you can create an alarm, you must enable billing alerts on your __Accounts Preferences__ page first
* Stored in CloudWatch `us-east-1`
* For overall worldwide costs
* Overall (not by project) alarms

## Cost explorer

* Graphical tool to visualize and analyze your costs and usage
* Review charges on accounts or organization
* Can forecast spending for next 3 months
* Get recommendations on which EC2 reserved instances to purchase
* Access to default reports
* API to build custom cost management applications
* __Reservation summary__

## AWS Budgets

* Similar to cost explorer, but
* Sends alarms when costs exceeding budget
* Types of budgets
	* Usage
	* Cost
	* Reservation
* For reserved instances (RI)
	* Track utilization
	* Supports EC2, Redshift, RDS, ElastiCache
* Up to 5 SNS notifications per budget
* Can filter by anything: Account, resource, service, tag, type, region, etc..
* Same options as costs explorer
* 2 budgets are free
* Then $0.02/day/budget

## Cost allocation tags

* Cost allocation tags: Tags that are set to be cost allocated
* Can enable detailed costing reports
* Shows up as columns in Reports
* Types
	* AWS Generated
		* Automatically applied
		* Starts with prefix: `aws:` (e.g.: `aws:createdBy`)
		* Not applied to resources created before activation
	* User created
		* Applied manually (or CLI)
		* Starts with prefix: `user:`
* Cost allocation tags only show up in the reports
* Can take 24 hours
* "Slice and dice costs by XXX" ––> Cost allocation tags

## [Back](../README.md)