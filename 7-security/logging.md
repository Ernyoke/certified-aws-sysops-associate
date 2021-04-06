# Logging in AWS

- To help with compliance requirements, AWS provides many service-specific security and audit logs
- Service Logs include:
    - CloudTrail trails - trace API calls
    - Config Rules - for config and compliance over time
    - CloudWatch Logs - for full data retention
    - VPC Flow Logs - IP traffic within a VPC
    - ELB Access Logs - metadata of requests made to our load balancers
    - CloudFront Logs - web distribution access logs
    - WAF Logs - full logging of all requests analyzed by the service
- Logs can be analyzed using AWS Athena if they are stored in S3
- We should encrypt logs in S3, control access using IAM and Bucket Policies and MFA