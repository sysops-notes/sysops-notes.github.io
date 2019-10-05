# [Route53](../README.md)

## Overview

* Managed DNS
* Common records
	* `A`: URL –> IPv4
	* `AAAA`: URL –> IPv6
	* `CNAME`: URL –> URL
	* `ALIAS`: URL –> AWS Resource
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
	* Free of charge
	* Support for native health checks

## Simple routing policy

* Maps a domain to one URL
* When: Redirect to a single resource
* Can't attach health checks
* If multiple values are returned, the __Client__ chooses one randomly

## Weighted routing policy

* Control the % of the requests that go to a specific endpoint
* Can be associated with health checks –> No traffic will be sent if instance is unhealthy
* Client is unaware of multiple IPs

## Latency routing policy

* Redirects to a server which has the least latency to you
* Asks which aws region is the IP is in

## Health checks

* Similar to ELB health checks
* After X health checks failed => `Unhealthy` (_Default 3_)
* After X health checks passed => `Healthy` (_Default 3_)
* Default health check interval: 30 seconds
* Fast health check interval: 10 seconds –> Higher costs
* About 15 health checkers will check the endpoint (_1 request every 2 seconds on average_)
* Can have
	* TCP health checks
	* HTTP health checks
	* HTTPS health checks (__No SSL validation__)
* Can integrate with CloudWatch
* __Can be linked to Route53 DNS queries!__

## Failover routing policy

* Must be a primary and a secondary endpoint
* Must have a health check created for the primary endpoint
* Automatically switches when health check failed

## Geolocation routing policy

* Different from Latency!
* Based on user's geological location
* Should create a default policy (in case the geo is not defined)
* E.g.: 
	* Traffic from UK goes to: X.X.X.X
	* Traffic from US goes to: Y.Y.Y.Y
	* Default goes to:         X.X.Y.Y 

## Multi value routing policy

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
1) Create your domain elsewhere
2) Create new hosted zone in R53
3) Copy the name servers from R53 hosted zone over where you registered your domain
4) Update the name servers there to use the one from 3)

## [Back](../README.md)