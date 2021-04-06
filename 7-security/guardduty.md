# GuardDuty

- Intelligent Threat discovery to protect AWS accounts
- Uses machine learning algorithms, anomaly detection, third party data
- Can be enabled with one click, it has a 30 days trial, no additional installation of software is required
- Input data to GuardDuty includes:
    - CloudTrail Logs: unusual API calls, unauthorized deployments
    - VPC Flow Logs: unusual internal traffic detection, unusual IP address detection
    - DNS Logs: compromised EC2 instances sending encoded data within DNS queries
- Notifies the users in case of findings
- Has integration with AWS Lambda