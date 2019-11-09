# [SNS](../README.md)

## General

* SNS is a web service that coordinates and manages the delivery or sending of messages to subscribing endpoints or clients
* Provides the ability to create Topic which is a logical access point and communication channel
* Supported Transport Protocols
	* `HTTP, HTTPS`
		* Subscribers specify a URL as part of the subscription registration
		* Notifications will be delivered through an HTTP POST to the specified URL
	* `Email, Email-JSON`
		* Messages are sent to registered addresses as email
		* Email-JSON sends notifications as a JSON object, while Email sends text-based email
	* `SQS`
		* Users can specify an SQS queue as the endpoint
		* SNS will enqueue a notification message to the specified queue
	* `SMS`
		* Messages are sent to registered phone numbers as SMS text messages
* Supported Endpoints
	* Email
	* Mobile Push Notifications
	* SQS Queues
	* SMS Notifications
	* HTTP, HTTPS endpoints
	* Lambda

## [Back](../README.md)