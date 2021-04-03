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
