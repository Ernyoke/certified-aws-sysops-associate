# AWS Inspector

- Only for EC2 instances
- Used to analyze known vulnerabilities and against unintended network accessibility
- An AWS Inspector agent must be installed on the EC2 instance
- We can define templates, which can contain: rule packages, duration, attributes, SNS topics
- There are no custom rules for Inspector, only AWS managed rules

## What does Inspector evaluate?

- For network assessment:
    - Network reachability
- For host assessments:
    - Common vulnerabilities and exposures
    - Center for Internet Security (CIS) benchmarks
    - Security best practices
    - Runtime behavior analysis