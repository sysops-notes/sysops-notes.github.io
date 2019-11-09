# [SQS](../README.md)

## General

* SQS is a highly available distributed queue system
	* Offers a reliable, highly-scalable, hosted queue for storing messages in transit between computers
	* Provides fault tolerant, loosely coupled, flexibility of distributed components of applications to send & receive without requiring each component to be concurrently available
	* Helps build distributed application with decoupled components
	* Requires no administrative overhead and little configuration
	* Supports the HTTP over SSL (HTTPS) and Transport Layer Security (TLS) protocols for security
* SQS provides two types of Queues – `Standard` & `FIFO`
* `ApproximateNumberOfMessages`: Returns the approximate number of messages available for retrieval from the queue.
* Messages that can't be processed (consumed) successfully can be forwarded for `Dead-letter queues`

## Standard Queues

* Redundant infrastructure
	* Offers reliable and scalable hosted queues for storing messages
	* Engineered to always be available and deliver messages
	* Provides ability to store messages in a fail safe queue
	* Highly concurrent access to messages
* __At-Least-Once delivery__
	* Ensures delivery of each message at least once
	* Stores copies of the messages on multiple servers for redundancy and high availability
	* Might deliver duplicate copy of messages
	* Applications should be designed to be idempotent with the ability to handle duplicate messages and not be adversely affected if it processes the same message more than once
* Message Attributes
	* SQS message can contain up to 10 metadata attributes.
	* Take the form of name-type-value triples
	* Can be used to separate the body of a message from the metadata that describes it.
	* Helps process and store information with greater speed and efficiency because the applications don’t have to inspect an entire message
* Batching
	* SQS allows send, receive and delete batching which helps club up to 10 messages in a single batch
	* Charging price for a single message helps lower cost and also increases the throughput
* Multiple writers and readers
	* Supports multiple readers and writers interacting with the same queue as the same time
	* Locks the message during processing, using `Visibility Timeout`, preventing it to be processed by any other consumer
* `Delay Queues`
	* Allows the user to set a default delay on a queue such that delivery of all messages enqueued is postponed for that time duration
* `Dead Letter Queues`
	* Queue for messages that were not able to be processed after a maximum number of attempts
* Use cases
	* Work Queues
	* Buffer and Batch Operations
	* Request Offloading
	* Fan-out
	* Auto Scaling

## FIFO Queues

* Provides enhanced messaging between applications with the additional features
	* FIFO (First-In-First-Out) delivery
	* Exactly-once processing
* Provides all the capabilities as Standard queues and improves upon and complements the standard queue
* Queue name should end with `.fifo`
* Not all the AWS Services support FIFO

## [Back](../README.md)