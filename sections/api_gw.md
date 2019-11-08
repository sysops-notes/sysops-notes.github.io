# [API Gateway](../README.md)

## General

* Fully managed service that makes it easy for developers to publish, maintain, monitor, and secure APIs
	* Can accept and process up to hundreds of thousands of concurrent API calls
	* Traffic management
	* Authorization and access control
	* Monitoring
	* API version management
* API Gateway acts as a proxy to the configured backend operations.
	* Lambda functions
	* AWS Step functions state machines
	* HTTP endpoints exposed through Elastic Beanstalk, ELB or EC2 servers
	* Non AWS hosted HTTP based operations accessible via public Internet
* Scales automatically to handle the amount of traffic the API receives
* Can accept any payloads. Typical data formats include:
	* JSON
	* XML
	* Query string parameters
	* Request headers
* Only exposes HTTPS endpoints
	* It does not support unencrypted (HTTP) endpoints
* No minimum fees or startup costs and charges only for the API calls received and the amount of data transferred out

## Metering

* Automatically meters traffic to the APIs and and lets you extract utilization data for each API key
* Can define plans that meter, restrict third-party developer access, configure throttling, and quota limits on a per API key basis

## Security

* Helps removing authorization concerns from the backend code
* Allows leveraging of AWS administration and security tools (`IAM`, `Cognito`)
* Can verify signed API calls on your behalf
* Supports custom authorizers written as Lambda functions and verify incoming bearer tokens
* Automatically protects the backend systems from distributed denial-of-service (DDoS) attacks
	* Counterfeit requests (Layer 7)
	* SYN floods (Layer 3)

## Resiliency
* Helps manage traffic with throttling so that backend operations can withstand traffic spikes
* Helps improve the performance of the APIs and the latency end users experience by __caching the output of API calls__

## Operations Monitoring

* Integrates with `CloudWatch` and provides a metrics dashboard to monitor calls to API services
* Integrates with `CloudWatch Logs` to receive error, access or debug logs
* Provides with backend performance metrics covering API calls, latency data and error rates

## Lifecycle Management

* Allows multiple API versions and multiple stages for each version simultaneously
* Saves the history of the deployments, which allows rollback of a stage to a previous deployment at any point, using APIs or console

## Throttling

* Provides throttling at multiple levels including global and by service call and limits can be set for standard rates and bursts
* Ensures that API traffic is controlled to help the backend services maintain performance and availability
* Tracks the number of requests per second. Any requests over the limit will receive a `429 HTTP` response

## Caching

* Provides API result caching
* Caching helps improve performance and reduces the traffic sent to the backend
	* If caching is not enabled and throttling limits have not been applied, then all requests pass through to the backend service until the account level throttling limits are reached
	* If throttling limits specified, then API Gateway will shed necessary amount of requests and send only the defined limit to the back-end
	* If a cache is configured, then API Gateway will return a cached response for duplicate requests for a customizable time, but only if under configured throttling limits
* All requests that are not intercepted by throttling and caching settings are sent to your backend operations

## Designed for Developers

* Allows you to specify a mapping template to generate static content to be returned, helping you mock APIs before the backend is ready
* Helps reduce cross-team development effort and time-to-market for applications and allow dependent teams to begin development while backend processes is still built

## [Back](../README.md)