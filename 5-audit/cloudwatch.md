# CloudWatch

## AWS CloudWatch Metrics

- CloudWatch provides metrics for every services in AWS
- A metric is a variable to monitor (CPUUtilization, NetworkIn, etc.)
- Metrics belong to namespaces
- A dimension is an attribute of a metric (instance id, environment, etc)
- We can have up to 10 dimensions per metric
- Metrics have a timestamp
- We can create a CloudWatch Dashboards with metrics

## AWS CloudWatch EC2 Detailed Monitoring

- EC2 instance metrics are reporting every 5 minutes
- With detailed monitoring we may get samplings every 1 minute
- Detailed monitoring also can improve the scalability of our application: use detailed monitoring if we want to more prompt scale of our ASG

## AWS CloudWatch Custom Metrics

- Possibility to define and send our own custom metrics to CloudWatch
- Ability to use dimensions (attributes) to segment metrics
- Metric resolution:
    - Standard: 1 minute
    - High resolution: up to 1 second (StorageResolution API parameter)
- To send a metric to CloudWatch, we can use the `PutMetricData` API
- We should use exponential back-off in case of throttle errors

## CloudWatch Dashboard

- Great way to setup dashboards for quick access to key metrics
- Dashboards are global
- Dashboards can include graphs from different regions
- We can change the time zone and time range of the dashboards
- We can setup automatic refresh (10s, 1m, 2m, 5m, 15m)
- Pricing:
    - 3 dashboards (up to 50 metrics) for free
    - $3/dashboard/month

## CloudWatch Logs

- Applications can send logs to CloudWatch using the SDK
- CloudWatch can collect logs from:
    - Elastic Beanstalk: collection of logs from the application
    - ECS: collections of logs from the containers
    - AWS Lambda: collection from function logs
    - VPC FLow Logs
    - API Gateway
    - CloudTrail based on filter
    - CloudWatch log agent
    - Route53: log DNS queries
- CloudWatch logs can be saved to:
    - Batch exporter to S3 archival
    - Stream to ElasticSearch cluster for further analysis
- Logs storage architecture:
    - Log groups: arbitrary name, usually representing an application
    - Log stream: instances within application/log files/ containers
- We can set log expiration policies
- Using the AWS CLI we can tail CloudWatch logs
- To send logs to CloudWatch, we have to make sure IAM permissions are correct
- Logs can be encrypted using KMS

## CloudWatch Log Metric Filter and Insights

- CloudWatch Logs can use filter expressions
- Metric filters can be used to trigger alarms
- CloudWatch Log Insights: can be used to query logs and add queries directly for CloudWatch Dashboards

## CloudWatch Alarms

- Alarms are used to trigger notifications for any metric
- Alarms can go to Auto Scaling, EC2 actions, SNS notifications
- Different ways of computing alarms: sampling, percentage max, min, etc.
- Alarm states:
    - OK
    - INSUFFICIENT_DATA
    - ALARM
- Period:
    - Length of time in seconds to evaluate the metric
    - High resolution custom metrics: we can only choose between 10 or 30 second
- Targets:
    - Stop, terminate, reboot or recover EC2 instances
    - Trigger auto scaling actions
    - Send notifications to SNS
- Alarms can be created based in CloudWatch Logs metrics filter
- CloudWatch does not test or validate the actions that are assigned
- In order to test an alarm, we can use the following command:
    ```
    aws cloudwatch set-alarm-state --alarm-name "name" --state-values ALARM --state-reason "testing"
    ```

## CloudWatch Events

- Source + Rule => Target should change in some ways
- Schedule: Cron jobs
- Event Pattern: rule to react to a service doing something, example: CodePipeline state change
- CloudWatch Events can trigger Lambda Functions, can send notifications to SNS, SQS, Kinesis Messages
- A event creates a small JSON document to give information about the change

## CloudTrail

- Provides governance, compliance and audit for an AWS account
- CloudTrail is enabled by default!
- Provides a history of events/API calls made within an AWS account
- It can put logs into CloudWatch Logs
- If a resource is deleted, we have to look into CloudTrail first
- CloudTrails shows the past 90 days of activity
- The default UI only shows create, modify and delete events
- CloudTrail Trail features:
    - Provides detailed list of all events
    - Ability to store these events in S3 for further analysis
    - It can be region specific or global
- CloudTrail Logs have SSE-S3 encryption when placed into S3

## Config

- Helps with audit and compliance on AWS resources
- Helps record configurations and changes over time
- Helps record compliance over time
- Config offers the possibility of storing Config data into S3, which allows to be queried by Athena
- We can receive alerts (SNS notification) for any changes
- Config is a per-region service
- Can be aggregated across regions and accounts

## Config Rules

- Config offers managed rules (over 75)
- We can make custom rules and define an AWS Lambda for it
- Rules can be evaluated based on the following triggers:
    - For each config change
    - At regular time intervals
- Pricing:
    - No free tier
    - 2$ USD per active rule per region per month

## CloudWatch vs CloudTrails vs Config

- CloudWatch:
    - Performance monitoring and dashboards
    - Events and alerting
    - Log aggregation and analysis
- CloudTrail:
    - Record API calls made within an account
    - We can define trails for specific resources
    - It is a global service
- Config:
    - Records configuration changes
    - Evaluates resources against compliance rules
    - Provides timelines of configuration changes and compliance
