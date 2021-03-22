# Deployment Automation - Elastic Beanstalk

- Elastic Beanstalk is a developer centric view of deploying an application on AWS
- It is free, but we pay for the underlying hardware
- It is a managed service and it will provide:
    - An instance configuration / OS is handled by Beanstalk
    - A deployment strategy which is configurable
- Beanstalk offers 3 architecture modes:
    - Single instance deployment: great for development
    - LB + ASG: great for production or pre-production web applications
    - ASG only: great for non-web apps in production
- Elastic Beanstalk has 3 components:
    - Application
    - Application version: each deployment gets assigned a version
    - Environment name (dev, test, prod)
- We deploy application versions to environments and promote application versions to the next environment
- We can rollback to previous application version
- We have full control over the lifecycle of the environments
- Supported platforms:
    - Go
    - Java SE/Tomcat
    - .NET on Windows Server with IIS
    - Node.js
    - PHP
    - Python
    - Ruby
    - Packer Builder
    - Docker: single container, multi-container
    - Custom: we can create our own environment

## Deployment Modes

- **All at once** (deploy all in one go): fastest, but instances are not available to serve traffic for a bit (downtime)
- **Rolling**: update a few instances at a time (bucket), and then move to the next bucket once the first one is healthy
- **Rolling with additional batch**: similar to rolling, but at the beginning new instances are created on order to have the application running at full capacity
- **Immutable**: creates new instances in a new ASG, deploys the newer version of the application to these instances and then swaps all the instances when everything is healthy

### All at once

- Fastest deployment
- Application has downtime
- Great for quick iterations in development environment
- No additional costs required

### Rolling

- Application is running bellow capacity during deployment
- The bucket size is configurable
- The application will run with both versions at the same time
- No additional costs required
- Deployment time is long

### ROlling with additional batches

- Application will not run under capacity
- The bucket size is configurable
- The application will run with both versions at the same time
- Small additional cost is encountered
- Deployment time is long
- Good deployment strategy for production

### Immutable

- Zero downtime required
- New code is deployed to new instances on a temporary ASG
- Implies higher costs, the capacity is doubled for the deployment time
- Longest deployment time
- Quick rollback in case of failure
- Great deployment strategy for production

## Blue/Green Deployment

- Not a direct feature of EB
- Zero downtime and release facility is required
- We have to create a new stage environment and deploy the newer version there
- The new environment (green) can be validated independently and roll back in case of any issues
- Using Beanstalk "swap ULR" when done with the environment test

## Beanstalk for Sysops

- Beanstalk can put application logs into CloudWatch Logs
- We manage the application, AWS will manage the underlying infrastructure
- We can put a Route 53 ALIAS or CNAME on top of Beanstalk URL
- Rolling deployment steps:
    1. EC2 has a base AMI (can be configured if needed)
    2. EC2 gets the new code of the application
    3. EC2 resolves the app dependencies
    4. Applications get swapped on the EC2 instance
- **Resolving dependencies can take a long time!**
    - Solution: use Golden AMI
- Golden AMI: if the application has a lot of application dependencies or OS dependencies, we can create a Golden AMI, which is a company specific AMI with:
    - OS dependencies
    - Preinstalled app dependencies
    - Preinstalled company-wide software
- By using a Golden AMI to deploy to Beanstalk in combination with blue/green deployments, our application wont need to resolve dependencies

## Troubleshooting Beanstalk

- If the health of an environment is red, we can try the following:
    - Review environment events
    - Pull logs to view recent log file entries
    - Roll back to a previous working version of the application
- When accessing external resources, we have to make sure the security groups are correctly configured
- In case of command timeouts, we can increase the deployment timeout time