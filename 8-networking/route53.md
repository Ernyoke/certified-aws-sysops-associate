# Route 53

- Route 53 is a managed DNS (Domain Name System)
- DNS is a collection of rules and records which helps clients to understand how to reach a server through its domain name
- In AWS, the most common records are:
    - A: hostname to IPv4
    - AAAA: hostname to IPv6
    - CNAME: hostname to hostname
    - Alias: hostname to AWS resource
- Route 53 can use:
    - Public domain we own
    - Private domain names that can be resolved in our VPCs
- Route 53 has advanced features such as:
    - Load balancing (through DNS - also called client load balancing)
    - Health checks
    - Route policy: simple, failover, geo-location, latency, weighted, multi value
- We pay $0.5 per moth per hosted zone

## DNS Record TTL

- DNS Record TTL - Time to Live: used to not overload the DNS server with queries. We can set a TTL for a DNS, while the TTL is not passed, the client wont hit the DNS server
- High TTL (example: 24h):
    - Less traffic on the DNS server
    - Possibility to have outdated records
- Low TTL (example: 60s):
    - More traffic on the DNS server
    - Records are outdated for more often
    - Easy to change records
- It is mandatory for each DNS record the specify a TTL

## CNAME vs Alias

- AWS resources (LB, CloudFront) expose a AWS hostname
- CNAME:
    - Points a hostname to any other hostname
    - **It only works for non root domains!**
- Alias:
    - Points a hostname to an AWS resource
    - Work for both root and non-root domains
    - They are free of charge
    - Have native health check capabilities

## Health Checks

- An IP is healthy if it fails X (default 3) health checks in a row
- After X health checks passed it can become available again
- Default health checks interval is 30 seconds, with possibility of fast health checks (up to 10s)
- About 15 health checkers will check the endpoint health => one request every 2 seconds on average
- We can have HTTP, TCP and HTTPS health checks (no SSL verification)
- Health checks can be integrated with CloudWatch
- Health checks can be linked to Route 53 DNS queries

## Simple Routing Policy

- We can use it when we need to redirect to a single resource
- We can't attach health checks to simple routing policies
- If multiple values are return, a radom IP is chosen by the client

## Weighted Routing Policy

- Controls the percentage of the requests which go to specific end-points
- Helpful to test a given percentage of traffic on new application version
- Helpful to split traffic between regions
- Can be associated with multiple health checks

## Latency Routing Policy

- Redirects to the server that has the least latency close to the user
- Latency is evaluated in terms of user to designated AWS region

### Failover Routing Policy

- Requires a primary record and a secondary record (disaster recovery)
- If the primary record fails, traffic is routed to the secondary record
- Requires a health check in order to work, failover is detected based on health checks

### Geo-location Routing Policy

- It is routing based on the user's location
- We can specify where the traffic should be routed if the user is requesting from an IP that originates from a country
- Requires a default policy (in case there is no match for a location)

### Geo-proximity Routing Policy

- It is also routing based on the user's location
- We can optionally choose to route more traffic or less to a given resource by specifying a value known as bias
- To use geo-proximity routing, we must use Route 53 traffic flow
- We create geo-proximity rules for our resources and specify one of the following values for each rule:
    - If we are using AWS resources, the AWS Region that you created the resource in
    - If we are using non-AWS resources, the latitude and longitude of the resource

### Multi Value Routing Policy

- Can be used when we need to route traffic to multiple resources and we want to associate Route53 health checks to the records
- It will return up to 8 healthy records for each Multi Value query
- It is not a substitute for Elastic Load Balancer!

## Route 53 as a Registrar

- A domain name registrar is an organization that manages the reservation of internet domain names. Some domain registrars are: GoDaddy, Google Domain and also Route 53
- It is possible to use a third party domain registrar with AWS. In order to use a domain bought from the third party, we have to do the following:
    1. Create a hosted zone in Route53
    2. Update NS records on 3rd party website to use Route 53 name servers


