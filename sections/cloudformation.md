# [Cloudformation](../README.md)

## Overview

* Infrastructure as code
* Declarative language
* How it works
	* Templates uploaded to S3
	* Can't edit an existing one. -> Need to re-upload with the changes!
	* Stacks are identified by a name
	* Deleting a stack deletes all resources created by it
* Deploying templates
	* Manual
		* CloudFormation Designer online
		* Console to input parameters
	* Automated
		* Editing yaml file
		* AWS CLI to deploy templates
* Config blocks:
	* `Resources`: AWS resource, mandatory
	* `Parameters`: Dynamic inputs
	* `Mappings`: static variables
	* `Outputs`: References to what had been created
	* `Conditionals`: List of conditions to perform resource actions
	* `Metadata`:
* Template helpers:
	* References
	* Functions

## Parameters

* Provide inputs to the template
* When/why use it:
	* If you want to reuse templates across company
	* If input cannot be determined ahead of time
	* If its going to change in the future
* How to reference:
	* By using the `Fn::Ref` function
		* `VpcId:	!Ref MyVPC`
* Pseudo parameters
	* AWS Provided
		* `AWS::AccountID`
		* `AWS::NotificationARNs`
		* `AWS::NoValue`
		* `AWS::Region`
		* `AWS::StackId`
		* `AWS::StackName`
* Can also reference resources

## Resources

* Core of every template (MANDATORY)
* Represents different AWS Components
* Can reference each other
* AWS figures out the order of creation/deletion/update
* Over 224 resources
* __Can't create dynamic number of resources!__ -> No code generation
* __Not all AWS resources are supported!__
	* Workaround: `AWS Lambda custom resource`
* Type Format:
	* `AWS::aws-product-name::data-type-name`
* Example:
	``` yaml
	MyInstance:
		Type: AWS::EC2::Instance
		Properties:
			AvailabilityZone: us-east-1a
			ImageId: ami-009d6802948d06e52
			InstanceType: t2.micro
			SecurityGroups:
				- !Ref SSHSecurityGroup
				- !Ref ServerSecurityGroup
	```

## Mappings

* Fixed variables
* All values are hardcoded
* Know values in advance, and they can be deducted from variables, e.g: Region, AZ, AWS Account etc...
* Example:
	``` yaml
	RegionMap:
		us-east-1:
			"32": "ami-6411e20d"
			"64": "ami-7a11e213"
		us-west-1:
			"32": "ami-c9c7978c"
			"64": "ami-cfc7978a"
	```
* Access:
	* Using the `Fn::FindInMap` function
	* `!FindInMap [ MapName, TopLevelKey, SecondLevelKey ]`
		* `ImageID: !FindInMap [ RegionMap, !Ref "AWS::Region", 32 ]`

## Outputs

* Declares optional output values that can be imported into another stacks __IF they are exported first!__
* Can't delete a stack which outputs are currently referenced!
* E.g.: VPC Stack -> Output (export) `vpc_id`, `subnet_ids` etc... 
* Export Example:
	``` yaml
	Outputs:
		OutputName:
			Description: Output description
			Value: !Ref someResourceName
			Export:
				Name: SuperUsefulResource
	```
* Import Example:
	``` yaml
	Resource:
		SomeName:
			Type: AWS::EC2::Instance
			Properties:
				AvailabilityZone: eu-west-1
				ImageId: ami-sad78qd1
				SecurityGroups:
					- !ImportValue: SuperUsefulResource
	```

## Conditionals

* Control the creation of resources or outputs based on a condition
* Condition can be whatever
	* `Fn::And`
	* `Fn::Equals`
	* `Fn::If`
	* `Fn::Not`
	* `Fn::Or`
* Definition
	``` yaml
	Conditions:
		CreateProdResources: !Equals [ !Ref EnvType, prod ]
	```
* Definition
	``` yaml
	Resource:
		SomeResource:
			Type: AWS::EC2::VolumeAttachment
			Condition: CreateProdResources
			Attributes:
				Some: value
	```

## Intrinsic functions

* `Fn::Ref`
	* Short: `!Ref`
	* Reference parameters: Return value
	* Reference resources: Return physical ID
* `Fn::GetAtt`: 
	* Short: `!GetAtt`
	* Usage:
		``` yaml
		NewVolume:
			Type: AWS::EC2::Volume
			Properties:
				AvailabilityZone:
					!GetAtt AnotherEC2Resource.AvailabilityZone
		```
* `Fn::FindInMap`
	* Short: `!FindInMap`
	* `ImageID: !FindInMap [ RegionMap, !Ref "AWS::Region", 32 ]`
* `Fn::ImportValue`
	* Short: `!ImportValue`
	* Importing values that are exported from other templates
* `Fn::Join`
	* Short: `!Join [ delimiter, [<coma-delimited list of values>] ]`
	* `a:b:c` <= `!Join [ ":", [ a, b, c ] ]`
* `Fn::Sub`
	* Short: `!Sub `
	* String must contain `${VariableName}` and will substitute them
* `Conditionals`

## User Data

* `Fn::Base64`
	``` yaml
	Resources:
		MyInstance:
			Type: AWS::EC2::Instance
			Properties:
				UserData: 
					Fn::Base64: |
						#!/bin/bash -xe
						yum update -y
						yum install -y httpd
	```

## cfn-init

* Must be in the metadata of the resource
* Helps make complex ec2 configurations readable
* EC2 instance will query the CloudFormation service to get the init data
* Logs to: `/var/log/cfn-init.log`
	``` yaml
	MyInstance:
			Type: AWS::EC2::Instance
			Properties:
				ImageId: ami-009d6802948d06e52
				UserData: 
					Fn::Base64:
						!Sub |
							#!/bin/bash -xe
							# Get the latest CloudFormation package
							yum update -y aws-cfn-bootstrap
							# Start cfn-init
							/opt/aws/bin/cfn-init -s ${AWS::StackId} -r MyInstance --region ${AWS::Region} || error_exit 'Failed to run cfn-init'
			Metadata:
				Comment: Install a simple Apache HTTP page
				AWS::CloudFormation::Init:
					config:
						packages:
							yum:
								httpd: []
						files:
							"/var/www/html/index.html":
								content: |
									<h1>Hello World from EC2 instance!</h1>
									<p>This was created using cfn-init</p>
								mode: '000644'
						commands:
							hello:
								command: "echo 'hello world'"
						services:
							sysvinit:
								httpd:
									enabled: 'true'
									ensureRunning: 'true'
	```

## cfn-signal & wait

* cfn-init won't know if it run successfully unless checking logs
* `cfn-signal` after cfn-init to fail or continue based on outcome
* Requires:
	* `WaitCondition`
		``` yaml
			SampleWaitCondition:
				CreationPolicy:
					ResourceSignal:
						Timeout: PT1M
						Count: 1
				Type: AWS::CloudFormation::WaitCondition
		```
	* In user data:
		``` shell
		/opt/aws/bin/cfn-signal -e $? --stack ${AWS::StackId} --resource SampleWaitCondition --region ${AWS::Region}
		```

### Wait condition failed

* Ensure AMI has the AWS CloudFormation scripts installed
* Check log outputs:
	* `/var/log/cloud-init.log`
	* `/var/log/cloud-init-output.log`
	* `/var/log/cfn-init.log`
* Verify instance is connected to the Internet 
* Stack creation fails
	* OnFailure options:
		* `Rollback` _(Default)_
		* `DoNothing`
		* `Delete`
* Stack update fails:
	* Auto rollback to previous state

## Nested stacks

* Isolate repeated patterns
* E.g.: LoadBalancer configuration
* Update parent stack -> Triggers update on nested stacks

## ChangeSets

* Will say what will change/created/deleted but won't say if it will work

## Retaining Data on Delete

* Can put `DeletionPolicy` on any resource
* `DeletionPolicy=Retain`: Will preserve/backup resource
* `DeletionPolicy=Snapshot `: Only works on: EBS volume, ElastiCache, RDS, RedShift
* `DeletionPolicy=Delete `: Default. Delete resource
	* Note: Delete S3 bucket, first need to delete it's content

## TerminationProtection

* Can prevent stack from accidental deletes

## [Back](../README.md)