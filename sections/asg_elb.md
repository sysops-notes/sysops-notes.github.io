# [Autoscaling Groups & Load balancers](../README.md)

## High availability & Scalability

### Scalability

* __Vertical__
	* Increase the size of the instance _(t2.micro -> t2.large)_
	* Common in non-distributed systems, such as databases
* __Horizontal__
	* Increase the number of instances/services _(from 1 to 6)_
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
* Provides SSL termination __(CLB & ALB)__
* Enforce stickiness with cookies
	* Cookie: `AWSELB`
* ELB - EC2 Load balancer
	* AWS managed
	* Types
		* `Classic` (v1) 2009
		* `Application` (v2) 2016
		* `Network` (v2) 2017
	* Can be `Private` _(aka internal)_ and `Public`
* `4xx` Client induced errors
* `5xx` Application induced errors
* `Cross Zone Load Balancing`: Distribute traffic across all registered instances in all enabled AZs

### Connections

* If the front-end connection uses TCP or SSL, then the back-end connections can use either TCP or SSL.
* If the front-end connection uses HTTP or HTTPS, then the back-end connections can use either HTTP or HTTPS.
* If you use TCP _(layer 4)_ for both front-end and back-end connections, the load balancer __forwards__ the request to the back-end instances __without modifying the headers.__
* If you use HTTP _(layer 7)_ for both front-end and back-end connections, the load balancer parses the headers in the request and __terminates the connection before sending the request__ to the back-end instances.

### SSL

* `Server Order Preference`
	* If enabled, LB will select the first cipher in its list that is also in the client's list of ciphers.
* You can create a load balancer with the following security features
	* `SSL Server Certificates`
	* `SSL Negotiation`
	* `Back-End Server Authentication`
* For old browsers, need to change the security policy to allow weaker ciphers
	* `ELBSecurityPolicy-TLS-1-0-2015-04`

### Application Load Balancer

* __Layer 7__ (HTTP/HTTPS/Websocket)
* URL Based routing (host or path)
* __Does not support static IP, but has a fixed DNS__
* Has port mapping feature -> redirect to any port
	* Multiple application on multi machine (`target groups`)
	* Multiple application on the SAME machine (`containers`)
* Route and Host based load balancing
* Stickiness via cookies
* The application servers don't see the clients' IP directly (its in `X-Forwarded-for` headers)
* Latency: ~400ms
* Adds a `Trace ID` to the header for each request: `X-Amzn-Trace-Id`
* Not integrated with X-Ray _(yet)_

### Network Load Balancer

* __Layer 4__ (TCP)
* __Millions of requests per seconds__ (High performance)
* Supports static & elastic IPs
	* 1 static IP per subnet
* __Doesn't support__ SSL termination
* Low latency: ~100ms
* Directly see clients' IP

### Stickiness
* Client redirected to the same instance behind an LB
* Cookie had an expiration date that can be controlled
* LB -> Attributes -> Enable stickiness (1sec - 7days)
* Can bring imbalance

### Exam tips
* NLB in front of ALB => ALB having a static IP
* Pre warming -> Support ticket
	* Duration of traffic
	* Expected requests per seconds
	* Size of requests

### Error codes

* `200`: Successful
* `4xx`: Unsuccessful at client side
	* `400`: Bad request
	* `401`: Not authorized
	* `403`: Forbidden
* `5xx`: Unsuccessful at server side
	* `500`: Internal server error -> Error with LB
	* `502`: Bad gateway
	* `503`: __Service unavailable__
	* `504`: Gateway timeout

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

### Cloudwatch Monitoring

* All metrics are directly pushed to CW
	* `BackendConnectionErrors`
	* `HealthyHostCount`/`UnHealthyHostCount`
	* `HTTPCode_ELB_4XX` => Malformed/cancelled __client__ request
	* `HTTPCode_ELB_5XX` => __LB/Instance__ errors OR LB cannot parse response
	* `HTTPCode_Backend_2XX` => OK
	* `HTTPCode_Backend_3XX` => Redirected
	* __`HTTPCode_Backend_4XX`__ => A __client error__ response sent from the registered instances.
	* __`HTTPCode_Backend_5XX`__ => A __server error__ response sent from the registered instances.
	* `Latency`
	* `RequestCount`
	* __`SurgeQueueLength`: Max is 1024, at max length connections dropped!__
	* __`SpilloverCount`: Number of connections dropped__

### LB Access Logs

* Stored in S3 _(need to enable in the LB `Attributes` - __Both in same region!__)_
* Encrypted by default
* No extra charge _(just S3 storage)_
* Lot of information __(Time, Client IP, Latencies, Request paths, Server response, Trace id)__

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
* Using step-scaling policy, you can specify the number of seconds that it takes for a newly launched instance to warm up
	* Until its specified warm-up time has expired, an instance is not counted toward the aggregated metrics of the Auto Scaling group
* Can create an ASG out of an Instance!
	* AutoScaling will automatically create the LC based on the instance
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


### Troubleshooting ASG

* `<number of instances> instance(s) are already running`: Increase __desired capacity__ of ASG
* Launching EC2 is failed:
	* SG is deleted
* `EC2 instance is in VPC. Failed to configure LB`
	* __Cause:__ The load balancer is in EC2-Classic but the Auto Scaling group is in a VPC
	* __Resolution:__ Make sure both are in the same network
* `EC2 instance is not in VPC. Failed to configure LB`
	* __Cause:__ The specified instance does not exist in the VPC.
	* __Resolution:__ Delete LB or Create new ASG
* ASG fails to launch for 24 hours -> `Administration suspension` All processes are suspended
	* Key pair is deleted

### CloudWatch Metrics

* Available ASG _(Opt-in)_ metrics - Every _1 minute_
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