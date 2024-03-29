# [Route53](../README.md)

## Overview

* Managed DNS
* Common records
	* `A`: URL –> IPv4
	* `AAAA`: URL –> IPv6
	* `CNAME`: URL –> URL
	* `ALIAS`: URL –> AWS Resource
	* `MX`: Mail Exchange
	* `NS`: Name server
	* `SOA`: Start Of Authority
	* `SPF`: Sender Policy Framework)
	* `SRV`: The first three values are decimal numbers representing priority, weight, and port. The fourth value is a domain name
* Available features
	* Load balancing (through DNS, also called as Client load balancing)
	* Health checks (although limited)
	* Routing policy (simple, failover, geolocation, latency, weighted, multi-value)
* $0.50/month per Hosted zone

## TTL

* Time To Live
* Time to cache the DNS response
* Hight TTL –> Light traffic to DNS server, potential to become outdated
* Low TTL –> High traffic to DNS server, quick to recognize changes

## CNAME vs ALIAS

* AWS Resources (LB, CF, etc...) expose an AWS URL but we want to expose it using our domain
* CNAME
	* Points URL to URL
	* __Only for NON ROOT Domain!__
	* `myapp.domain.com` to `s0m3Rand0mID.elb.amazonaws.com`
* ALIAS
	* Points URL to AWS Resource only
	* __Works for BOTH root AND non-root Domain!__
	* `myapp.domain.com` to `s0m3Rand0mID.elb.amazonaws.com`
		* or
	* `domain.com` to `s0m3Rand0mID.elb.amazonaws.com`
	* Free of charge
	* Support for native health checks

## Health checks

* Similar to ELB health checks
* After X health checks failed => `Unhealthy` _(Default 3)_
* After X health checks passed => `Healthy` _(Default 3)_
* Default health check interval: 30 seconds
* Fast health check interval: 10 seconds –> Higher costs
* About 15 health checkers will check the endpoint _(1 request every 2 seconds on average)_
* If more than 18% of the health checkers deems the endpoint healthy, Route53 treats it healthy
* Health checker must return __HTTP status code of 2xx or 3xx! to deem healthy__
* Can have
	* TCP health checks
	* HTTP health checks
	* HTTPS health checks _(No SSL validation)_
* Can integrate with CloudWatch
* __Can be linked to Route53 DNS queries!__

## Routing policies

### Simple routing policy

* Maps a domain to one URL
* When: Redirect to a single resource
* Can't attach health checks
* If multiple values are returned, the __Client__ chooses one randomly

### Weighted routing policy

* Control the % of the requests that go to a specific endpoint
* Can be associated with health checks –> No traffic will be sent if instance is unhealthy
* Client is unaware of multiple IPs

### Latency routing policy

* Redirects to a server which has the least latency to you
* Asks which aws region the IP is in

### Failover routing policy

* Must be a __primary__ and a __secondary__ endpoint
* Must have a health check created for the primary endpoint
* Automatically switches when health check failed

### Geolocation routing policy

* Different from Latency!
* Based on user's geological location
* Should create a default policy (in case the geo is not defined)
* E.g.: 
	* Traffic from UK goes to: `X.X.X.X`
	* Traffic from US goes to: `Y.Y.Y.Y`
	* Default goes to:         `X.X.Y.Y`

### Multi value routing policy

* Use when want to route to multiple instances & associate a health check
* Up to 8 healthy records are returned for each multi value query
* Its not a substitute for LB
* They all have the same TTL

## 3rd Party domains

* Route53 is also a __Registrar__
* DNS Registrar: an organization that manages the reservation of Internet domain names
* Famous registrars:
	* GoDaddy
	* Google domains
	* Route53 (AWS)
* __Domain registrar != DNS__
* How to:
	* Create your domain elsewhere
	* Create new hosted zone in R53
	* Copy the name servers from R53 hosted zone over where you registered your domain
	* Update the name servers there to use the one from 3)

## Route 53 Split-view (Split-horizon) DNS

* Route 53 Split-view (Split-horizon) DNS enables you to access an internal version of your website using the same domain name that is used publicly
* You can maintain both a private and public hosted zone with the same domain name for split-view DNS with Route 53
* DNS queries will respond with answers based on the source of the request

## [Back](../README.md)