# [Security and compliance](../README.md)

## Shared Responsibility Model

![Model](https://d1.awsstatic.com/security-center/Shared_Responsibility_Model_V2.59d1eccec334b366627e9295b304202faf7b899b.jpg "Responsibility Model")

## AWS Shield & AWS WAF

* DDoS: Distributed Denial of Service

### Protection against DDoS

* AWS Shield Standard
	* Protects against DDoS without any additional costs
	* Enabled by default
* AWS Shield Advanced
	* 24/7 premium DDoS protection
* AWS WAF
	* Filter specific requests based on rules
* CloudFront and Route53
	* Availability protection using global edge network
	* When combined with AWS SHield, provides attack mitigation at edge
* Be ready to scale (AWS Auto Scaling)
* Separate static resources (S3/CloudFront) from dynamic (EC2/ALB)

### AWS Shield

* Shield standard
	* Free service, activated for everyone
	* Protects from
		* SYN/UDP floods
		* Reflections attacks
		* Layer 3/4 attacks
* Shield advanced
	* Optional DDoS mitigation (can cost up to $3k per month per organization)
	* Protects against more sophisticated attacks on CloudFront, C/A/NLB, Route53 etc...
	* 24/7 to AWS DDoS Response Team (DRP)
	* Protects against higher fees during DDoS attacks

### AWS WAF

* Define customizable web security rules
	* Control which traffic to allow/deny
	* Rules can include: HTTP header, HTTP body, URI strings...
	* Protect against SQL injection
	* Protect against XSS (Cross Site Scripting)
	* Protect against bots, user agents...
	* Size constraints
	* Geo match
* Can deploy on CloudFront, ALB, API Gateway
* Leverage existing rules on marketplace

## Pentesting

* Permission required from AWS
* Must use root credentials
* Must perform on yourself, not by 3rd party
* For a 3rd part, need to fill a form
* Can only test against:
	* EC2, ELB, RDS, Aurora, CloudFront, API Gateway, Lambda and Lightsail
* __Cannot__ test against:
	* nano / micro / small kind of EC2 instances
* 2 business days to approve the form

## AWS Inspector

* __Only for EC2 instances!__
* Analyze against known vulnerabilities
* Analyze against unintended network accessibility
* __AWS Inspector agent must be installed on the OS!__
* Define template (rules package, duration, attributes, SNS topics)
* No custom rules (can only use AWS managed ones)

## Logging for security and compliance

* Service logs
	* CloudTrails: Trace API calls
	* Config Rules: Compliance over time
	* CloudWatch Logs: data retention
	* VPC Flow Logs: IP Traffic withing your VPC
	* ELB Access Logs: Metadata of request to the LBs
	* CloudFront Logs: Web distribution logs
	* WAF Logs: Full logging of all requests analyzed by the service
* All these can be put into s3 –> Analyze with `AWS Athena`
* Encrypt bucket + use IAM & bucket policies to secure, MFA delete
* Transition to glacier with lock policy

## GuardDuty

* Intelligent threat discovery to protect AWS account
* Uses ML algorithms, anomaly detection, 3rd party data
* One click to enable (30 days trial), no need to install anything
* Input data:
	* CloudTrail logs
	* VPC Flow logs
	* DNS logs
* Integrates with Lambda

## Trusted advisor

* No need to install anything
* High level AWS account assessment
* Provides recommendations
	* Cost optimization _(need to upgrade support plan to access)_
	* Performance _(need to upgrade support plan to access)_
	* Security _(some parts are included, for the rest need to upgrade support plan)_
	* Fault tolerance _(need to upgrade support plan to access)_
	* __Service limits__ _(included)_
* __Core Checks__ and recommendations - all customers (included)
* __Full Trusted Advisor__ Only for Business & Enterprise support plans
	* Ability to set CloudWatch alarms when reaching limits
* __Can enable weekly email notification through the UI!__

## KMS

* Key management service
* Supports only symmetric keys
* Fully integrated with IAM for authorization
* Seamlessly integrated with many services (EBS, S3, Redshift, RDS, SSM...)
* Can also use the cli to encrypt ourselves
* 101
	* Need to share sensitive information –> KMS
* CMK (Customer Master Key)
	* used to encrypt data
	* can never be retrieved by the user
	* can be rotated for extra security
* __Can only encrypt up to 4 KB data per call__
* If data > 4 KB
	* __Have to use envelope encryption__
* Give access to KMS to someone
	* Key policy allows user
	* IAM policy allows API calls
* Ability to fully manage keys & policies
	* Create
	* Rotate policies
	* Disable
	* Enable
* Able to audit key usage in CloudTrail
* 3 types of Customer Master Keys (CMK)
	* AWS Managed Service default: Free
	* User keys: $1/month
	* User keys imported (must be 256-bit symmetric key): $1/month
* Fee for API calls to KMS ($0.03/10000 calls)

### Encryption

* Requires migration (through snapshot/backup)
	* EBS volumes
	* RDS databases
	* ElastiCache
	* EFS
* Can encrypt in place
	* S3

## Cloud HSM

* Hardware Security Model
* AWS provisions the encryption hardware only (KMS manages to software for encryption)
* Dedicated hardware
	* tamper resistant
	* Can chose HW compliance
		* eg: `FIPS 140-2 Level 3 compliance`
* You manage your own encryption keys entirely _(single tenant key storage unlike KMS which is multi tenant)_
* CloudHSM clusters are spread across multi AZ (HA)
* Supports symmetric and asymmetric keys!
* No free tier
* Must use the CloudHSM Client software (no cli)
* Deployed and managed in your VPC (accessible from within or peered VPCs)

## IAM & MFA

* MFA adds a level of security
* Accepts both virtual and physical MDA devices
	* Virtual: Google Authenticator / Authy
* Can be configured through the dashboard or CLI for root
* Can be configured for normal users
* Credentials report
	* CSV report on all IAM users and credentials
	* Shows who has MFA enabled

## IAM Pass role

* Ability to assign a role to an instance/resource

## STS

* Security Token Service
* Allows to grant limited and temporary access to AWS resources
* Mostly used for cross account access
* Federation (AD)
	* Provides non AWS user temporary access by linking Active Directory credentials
	* Uses SAML (Security Assertion Markup Language)
	* Allows SSO (Single Sign On) which enables users to log in to AWS console without assigning IAM credentials
* Federation with 3rd part providers (Cognito)
	* mainly in web and mobile applications
	* makes use of Facebook/Google/Amazon  etc to federate them

### Cross account access

* Define IAM role for another account
* Define which accounts can assume this role
* Use STS to retrieve credentials and impersonate this IAM role you have access to _(Assume role API)_
* Temporary credentials can be valid from 15 min to 12 hours (cannot be longer than the role allows)

## Federation

* Lets users outside of AWS to assume temporary role to access AWS resources
* Federation assumes a form of a 4rd party authentication
	* LDAP
	* MS Active directory (~SAML)
	* Singe Sign On
	* Open ID
	* Cognito
* User management is done outside of AWS (–> don't need to create users in AWS)

### SAML Federations for Enterprises

* Integrate with AD / ADFS / any other SAML 2.0
* Auth with your Identity provider
* That returns a SAML assertion (token)
* This is recognized by AWS and lets you
	* Use the CLI (STS)
	* Access console (SSO, in the background uses STS)

### Custom Identity Broken

* If you don't have an identity provider compatible with SAML 2.0
* You have to implement the identity provider
* Lot more manual work
	* This is like a super user, as it can
		* Get sts credentials for any policy/user

### AWS Cognito Federated Identity Pools

* For public applications
* Goal
	* Provide direct access o AWS resources from the Client side
* How
	* Log into federated identity provider - or remain anonymous
	* get temporary credentials back from the federated identity pool (from Cognito)
	* these come with pre-defined IAM policies

## AWS Artifact

* not really a service
* Portal that provides customers with on-demand access to AWS compliance documentation and AWS agreements
* Artifact reports
* Artifact agreements
> How can I find compliance documentation on AWS? –> AWS Artifact 

## [Back](../README.md)