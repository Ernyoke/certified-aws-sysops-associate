# DDoS Protections

- AWS Shield Standard: protection against DDoS attacks for all customers at no additional cost
- AWS Shield Advanced: 24/7 premium DDoS protection
- AWS Web Application Firewall (WAF): Filter specific requests based on rules
- CloudFront adn Route 53:
    - Availability protection using global edge network
    - Combined with AWS Shield, provides attack mitigation at the edge
- Scaling: leverage AWS Auto Scaling
- Separate static resources (S3/CloudFront) from dynamic ones (EC2/ALB)

## AWS Shield

- AWS Shield Standard:
    - Free service that is activated for every AWS customer
    - Provides protection from attacks such as SYN/UDP floods, reflection attacks and other layer 3/4 attacks
- AWS Shield Advanced:
    - Optional DDoS mitigation service ($3000 per month per organization)
    - Protects against more sophisticated attacks on CloudFront, Route 53, AL7-security/B, EC2
    - 24/7 access to AWS DDoS response team (DRP)

## AWS WAF - Web Application Firewall

- Protects a web application from common web exploits
- We can define custom web security rules:
    - Control which traffic to allow or block to the web application
    - Rules based on: IP address, HTTP headers, HTTP body, URI strings
    - Protection from common attacks: SQL injections, XSS
    - Size constraints
    - Geo match
- Can be deployed on CloudFront, ALB and API Gateway
- We can leverage existing marketplace rules