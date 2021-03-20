# SSM - AWS Systems Manager

- SSM helps manage EC2 and On-Premise systems at scale
- Offers operational insights about the state of the infrastructure
- Problems can be easily detected with it
- Offers patching automation for enhanced compliance
- Works on both Windows and Linux
- Integrates with CloudWatch metrics and dashboards
- Integrates with AWS Config
- It is a free service

## SSM Features

- Resource Groups
- Insights:
    - Insights Dashboard
    - Inventory: discover and audit the software installed
    - Compliance
- Parameter Store
- Actions:
    - Automation (shut down EC2 instances, create AMIs)
    - Run Command
    - Session Manager
    - Patch Manager
    - Maintenance Windows
    - State Manager: define and maintaining configuration of OS and applications

## How Systems Manager Works

- Instances need to install the SSM agent
- This is installed by default on Amazon Linux AMI and on some Ubuntu AMIs

## AWS Tags

- We can add text key-value pairs called tags to many AWS resources
- Tags have free naming conventions, common tags are: Name, Environment, Team, etc.
- They are used for:
    - Resource grouping
    - Automation
    - Cost allocation

## AWS Resource Groups

- We can create, view or manage logical group of resources using tags
- AWS Resource Groups allow creation of logical groups of resources such as:
    - Applications
    - Different layers of an application stack
    - Production/Development environments
- AWS Resource Groups is a regional service

## SSM Documents

- Documents can be in JSON or YAML
- We can define parameters, actions

## SSM Run Command

- Execute a document or just run a command
- This can be done across multiple instances using resource groups
- There is a possibility to have rate control / error control
- It is integrated with IAM and CloudTrail

## Using SSM to Patch Instances

- Inventory: list software on an instance
- Inventory + Run Command: patch software
- Patch manager + Maintenance Window: path OS
- Patch manager: gives compliance
- State manager: ensures that instances are in a consistent state (compliance)

## AWS Systems Manager Session Manager

- Allows to start a secure shell on a VM
- **Does not use SSHe access and bastion hosts**
- Only works for EC2 for now
- Log actions done through secure shells to S3 and CloudWatch Logs
- IAM permissions: access SSM + write to S3 + write to CloudWatch
- CloudTrail can intercept StartSessions events
- AWS Secure Shell compared to SSH:
    - No need to open port 22 at all anymore
    - No need fot a bastion host
    - All commands are logged to S3/CloudWatch
    - Access to Secure Shell is done through User IAM, not SSH keys