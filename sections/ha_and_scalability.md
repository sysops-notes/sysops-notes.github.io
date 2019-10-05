# [Autoscaling Groups & Load balancers](../README.md)


## High availability & Scalability

### Scalability

* Vertical
	* Increase the size of the instance (t2.micro -> t2.large)
	* Common in non-distributed systems, such as databases
* Horizontal
	* Increase the number of instances/services (from 1 to 6)
	* Common in distributed systems

### High availability

* Running the application in at least 2 data centers

### AWS Terminology

* `Scale up/down`     = Vertical scaling 
* `Scale out/in`      = Horizontal scaling 
* `High availability` = Multi AZ

## Load balancer

* Spread load across multiple downstream instances
* Seamlessly handle failures for downstream instances
* Regular health checks
* Provides SSL termination (CLB & ALB)
* Enforce stickiness with cookies
* ELB - EC2 Load balancer
	* AWS managed
	* Types
		* Classic (v1) 2009
		* Application (v2) 2016
		* Network (v2) 2017
	* Can be Private and Public
* 4xx client induced errors
* 5xx application induced errors

### Application Load Balancer

* Layer 7 (HTTP/HTTPS/Websocket)
* URL Based routing (host or path)
* Does not support static IP, but has a fixed DNS
* Has port mapping feature -> redirect to any port
* Multiple application on multi machine (`target groups`)
* Multiple application on the SAME machine (`containers`)
* Route and Host based load balancing
* Stickiness via cookies
* The application servers don't see the clients' IP directly (its in `X-Forwarded-for` headers)
* Latency: ~400ms
* Adds a trace id to the header for each request: `X-Amzn-Trace-Id`
* Not integrated with X-Ray (yet)

### Network Load Balancer

* Later 4 (TCP)
* Millions of requests per seconds (High performance)
* Supports static & elastic IPs
	* 1 static IP per subnet
* No SSL termination
* Low latency: ~100ms
* Directly see clients' IP

### Stickiness
* Client redirected to the same instance behind an LB
* Cookie had an expiration date that can be controlled
* LB -> Attributes -> Enable stickiness (1sec - 7days)
* Can brin imbalance
 
### Exam tips
* NLB in front of ALB => ALB having a static IP
* Pre warming -> Support ticket
	* Duration of traffic
	* Expected requests per seconds
	* Size of requests

### Error codes

* `200`: Successful
* `4xx`: Unsuccessful at client side
	* `400`: bad request
	* `401`: not authorized
	* `403`: forbidden
* `5xx`: Unsuccessful at server side
	* `500`: internal server error -> Error with LB
	* `502`: bad gateway
	* `503`: __Service unavailable__
	* `504`: gateway timeout

### SSL for old browsers

* Change security policy to allow weaker ciphers
	* `ELBSecurityPolicy-TLS-1-0-2015-04`

### Troubleshooting

* Security groups
* Health checks
* Sticky sessions
* Cross zone balancing enabled
* (Internal LB for private)
* (Deletion protection)
* Using metrics:
	* `HTTP 400: BAD REQUEST` => Client malformed request
	* `HTTP 503: Service unavailable` => Look for healthy host count! (there are instances and configured)
	* `HTTP 504: Gateway timeout` => Keep alive timeout is greater than the idle timeout setting

### LB Monitoring

* All metrics are directly pushed to CW
	* `BackendConnectionErrors`
	* `HealthyHostCount`/`UnHealthyHostCount`
	* `HTTPCode_Backend_2xx`= Successful
	* `HTTPCode_Backend_3xx`= Redirected
	* __`HTTPCode_ELB_4xx`= Client error__
	* __`HTTPCode_ELB_5xx`= Server error__
	* `Latency`
	* `RequestCount`
	* __`SurgeQueueLength`: Max is 1024, at max length connections dropped!__
	* __`SpilloverCount`: Number of connections dropped__

### LB Access Logs

* Stored in S3 (need to enable in the LB `Attributes` - Both in same region!)
* Encrypted by default
* No extra charge (just S3 storage)
* Lot of information (Time, Client IP, Latencies, Request paths, Server response, Trace id)

## AutoScaling Group

* Scale in/out instances
* Automatically register new instances with ELB
* Minimum size
* Desired capacity
* Maximum size
* Launch configuration
	* AMI + instance type
	* EC2 user data
	* EBS volume
	* Security Groups
	* SSH key pair
	* Min/Max size
	* Network & Subnet
	* Load balancer
	* IAM role
	* Scaling policies
* Scaling is based on CloudWatch Alarms
	* Built-in + better*
		* CPU/Network/Time schedule...
		* Target average X usage
		* Number of requests
		* Average Network In/Out
	* Custom
		* Send custom metrics to CW
* Will not reboot instances, only terminate unhealthy ones

### Scaling processes

* Can suspend processes: ASG won't do those actions!
* Processes
	* __`Launch`: Add new instance, increase capacity__
	* __`Terminate`: Remove instance, decrease capacity__
	* __`HealthCheck`: Check the health of the instance__
	* __`ReplaceUnhealthy`: Terminate unhealthy and replace it__
	* __`AZRebalance`: Balance the number of instances in across AZs__
	* `AlarmNotification`: Accept notification from CW
	* `ScheduledAction`: Performs scheduled actions that you create
	* `AddToLoadBalancer`: Add instance to the LB or Target Group
* Note on `AZRebalance`
	* If `Launch` is suspended, `AZRebalance` is implicitly disabled
	* If `Terminate` is suspended, `AZRebalance` can create, but won't be able to terminate (rebalance can add go up with __10%__ of max)


### Troubleshoot ASG

* `<number of instances> instance(s) are already running`: Increase __desired capacity__ of ASG
* Launching EC2 is failed:
	* SG is deleted
* ASG fails to launch for 24 hours -> `Administration suspension` All processes are suspended
	* Key pair is deleted

### CW Metrics for ASG

* Available ASG (__Opt-in__) metrics - Every _1 minute_
	* `GroupMinSize`
	* `GroupMaxSize`
	* `GroupDesiredCapacity`
	* `GroupInServiceInstances`
	* `GroupPendingInstances`
	* `GroupStandbyInstances`
	* `GroupTerminatingInstances`
	* `GroupTotalInstances`
* Underlying EC2 metrics
	* Basic (free) - _5 minutes_
	* Detailed (extra costs) - _1 minutes_

## [Back](../README.md)