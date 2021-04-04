# Cost Management

## AWS BIlling Alarms

- Billing data metric is stored in CloudWatch us-east-1
- Billing data are for overall worldwide AWS costs
- It's for actual cost, not for project costs

## AWS Cost Explorer

- A graphical tool to view and analyze cost and usage
- We can review charges and cost associated with an AWS account or organization
- It can forecast spending for the next 3 months
- It can give recommendation for which EC2 Reserved instances to purchase
- It provides access to default reports
- It provides an API to build cost management applications
- Provides reservation summary and recommendations - these are reports concerning EC2 reserved instances purchases and recommendation regarding which type of EC2 instance to purchase

## AWS Budgets

- We can create budgets and send alarms when costs exceed the budget
- Provides 3 types of budgets:
    - Usage
    - Cost
    - Reservation
- For reserved instances we get:
    - Track utilization
    - Support for EC2, ElasticCache, RDS, Redshift
- Provides up to 5 SNS notifications per budget
- We can filter by: service, linked account, tag, purchase options, instance type, region, AZ, API operation, etc.
- 2 budgets are free, then %0.02/day/budget

## AWS Cost Allocation Tags

- With tags we can track resources which relate to each other
- With cost allocation tags we can enable detailed costing reports
- Cost allocation tags are just like default tags, but they show up as columns in reports
- There are 2 types of cost allocation tags:
    - AWS generated tags:
        - Automatically applied to the resources we create
        - They start with the prefix `aws` (example: `aws:createdBy`)
        - They are not applied to resources created before activation
    - User tags:
        - Defined by the user
        - They start with the prefix `user`
- Cost allocation tags appear only in the Billing Console
- It can take 24 hours to show up in the billing reports
