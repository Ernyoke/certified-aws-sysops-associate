# EC2

## Placement Groups

- Sometimes we want control over the EC2 instance placement strategy
- This strategy can be defined using placement groups
- When we create a placement group, we specify one the following strategies:
    - **Cluster**: places instances into a low latency group in a single AZ
    - **Spread**: spreads instances across underlying hardware (limitation: max 7 instances per group per AZ)
    - **Partition**: similar to spread, instances are spread across many different partitions (different sets of racks) within an AZ. Can scale up to hundreds of EC2 instances per group. Recommended for Hadoop, Cassandra, Kafka

### Cluster Placement Group

- All instances are placed on the same rack, same AZ
- Pros: great network (10Gbps bandwidth between instances)
- Cons: if the rack fails, all instances fails at the same time
- Use cases:
    - Big Data job that needs to complete fast
    - Application that needs extremely low latency and high network throughput

### Spread Placement Group

- All the EC2 instances are located on different hardware
- Pros:
    - Can span across AZs
    - Reduced risk for simultaneous failure
- Cons:
    - Limited to 7 instances per AZ per placement group
- Uses cases:
    - Application that needs to maximize high availability
    - Critical applications where each instance must be isolated from failure from each other

### Partition Placement Group

- Partitions are sets of racks
- We can create up to 7 partitions in case of partition placement group
- We can have hundreds of EC2 instances as part of a placement group
- The instances in a partition do not share racks with the instances in the other partitions
- A partition failure can affect many EC2 instances but wont affect other partitions
- EC2 instances get access to the partition information as metadata
- Use cases: HDFS, HBase, Cassandra, Kafka

## Shutdown Behavior

- Shutdown Behavior: how should an instance react when shutdown is done using the OS?
    - Stopped: default
    - Terminated
- Shutdown Behavior is not applicable from AWS Console or AWS API, itt applies to shutdowns from inside the VM
- CLI Attribute: `InstanceInitiatedShutdownBehavior`
- Termination protection: protects against accidental termination in AWS Console or CLI
- *We have an instance where shutdown behavior is "terminate" and termination protection is enabled. When we shutdown the instance from the OS, the instance still be terminated*

## EC2 Launch Troubleshooting

- **# InstanceLimitExceeded error**: means that we have reached the limit of max number of instances per region. (As of September 2019, this limit is counted in vCPU instead of instances, default is 32). 
    - Solution: launch the instance in a different region or create a support ticket to increase the limit. Default limit is: 20
- **# InsufficientInstanceCapacity**: means AWS does not have that much On-Demand capacity in the particular AZ in order for the instance to be launched
    - Solutions:
        - Wait for a few minutes before requesting again
        - If more than once instance is requested, we can break down the request by creating the instances one by one
        - If urgent, submit a request for a different instance type and upgrade it afterwards
- **# Instance terminates immediately**:
    - EBS volume limit is reached
    - EBS snapshot is corrupt
    - The root EBS volume is encrypted and we don't have permission to access the KMS key for decryption
    - The instance store-backed AMI that we are using to launch the instance is missing a required part (an image.part.xxx file)
- To find the exact reasons: check the EC2 console of AWS - Instances - Description tab - State transition reason

## EC2 SSH Troubleshooting

- We have to make sure the private key (pem file) has 400 permissions, else we get *Unprotected Private Key File error*
- We have to make sure the username for the OS is given correctly when logging via SSH, else we get a "Host key not found" error
- Connection timeout reasons:
    - SG is not configured correctly
    - CPU load of the instance is high

## EC2 Instance Launch Types

- On Demand Instances: short workload, predictable pricing
- Reserved (minimum 1 year)
    - Reserved Instances: long workloads
    - Convertible Reserved Instances: long workload with flexibly instance types
    - Scheduled Reserved Instances
- Spot Instances: short workloads for cheap. Instance can be lost over time
- Dedicated Instances: no other customers will share the hardware
- Dedicated Hosts: book an entire physical server, we can control instance placement

### EC2 On Demand

- Payt for what we use
- Billing is happening per second after the first minute
- Has the highest cost but does not require any upfront commitment
- Recommended for short-term and un-interrupted workloads where we can't predict how the application will behave

### EC2 Reserved Instances

- Up to 75% discount compared to On Demand
- Pay upfront for what we use with long term commitment
- Reservation period can be 1 or 3 years
- We can reserve a specific instance type
- Recommended for steady state usage applications (example: database)

### Convertible Reserved Instances

- Similar as Reserved Instances
- We can change the EC2 instance type
- Up to 54% discount

### Scheduled Reserved Instances

- Instances which are reserved and are launched within a time window
- Recommended when we require compute for a fraction of the day, week, month

### Dedicated Hosts

- Physical dedicated EC2 server for our use
- Offers full control of EC2 instance placements
- Offers visibility into the underlying sockets/physical cores of the hardware
- Can be allocated for 3 years period reservation
- More expensive
- Useful for software that have complicated licensing model (BYOL - Bring Your Own License) or string regulatory compliance needs

### Dedicated Instances

- Instances are running on hardware dedicated for the account
- May share hardware with other instances from the same account
- We have no control over instance placement (can move hardware after Stop/Start)

### EC2 Spot Instances

- Can get a discount of up to 90% compared to On Demand instances
- We can define a max spot price and get he instance of our price is bigger than the current price
- If the current spot price goes beyond our max price, we can choose to stop or terminate the instance within 2 minutes grace period
- If we don't want our spot instance to be reclaimed by AWS, we can use a **Spot Block**
    - We can block a spot instance during a specified time frame (1 to 6 hours) without interruptions
    - In rare situations the instance may be reclaimed
- Use cases for spot instances: batch jobs or workloads that are resilient to failure
- We can launch spot instances with a spot request. A spot request contains the following information:
    - Maximum price
    - Desired number of instances
    - Launch specification
    - Request type: on-time, persistent
    - Valid from, valid until
- Request types:
    - One time request: as soon as the request is fulfilled, the request will go away
    - Persistent request: the number of instances is attempted to be kept even if some instances are reclaimed, meaning that the request will not go away as soon as it is completed first time
- Canceling a spot instances: in order ot cancel a spot instance, it has to be in an **open**, **active** or **disabled** state.
- Cancelling a spot request, it will not terminate the instances themselves. In order to terminate instances, first we have to terminate the spot request, if there is one active

### Spot Fleets

- Spot Fleet - set of spot instances + (optional) on-demand instances
- The spot fleet will try to meet the target capacity with price constraints
- A launch pool can have the following can have different instance types, OS, AZ
- We can have multiple launch pools, so the fleet can choose the best
- Spot fleet will stop launching instances the target capacity is reached
- Strategies to allocate spot instances:
    - **lowestPrice**: the spot fleet will launch instances from the pool with the lowest price
    - **diversified**: distribute instances across all pools
    - **capacityOptimized**: launch instances based on the optimal capacity for the number of instances
- Spot fleets allow us to automatically request spot instances with the lowest price

## EC2 Instance Types

- R: applications that need a lot of RAM, example in-memory caches
- C: applications that need a good CPU, example compute/databases
- M: applications that are balanced, example general web-app
- I: applications that need a good local I/O (instance storage), example databases
- G: applications that need a GPU, example video-rendering, ML
- T2/T3: burstable instances
- T2/T3 - unlimited: unlimited CPU burst, we get charged for bursts

### Burstable Instances (T2/T3)

- Burst: when a machine needs to process something unexpected (a spike), it can burst the CPU power
- If the machine bursts, it will utilize burst credits
- If the credits are gone, the machine CPU performance will suffer
- If the machine is stopped, burst credits do accumulate over time
- The bigger the instance, the faster we can earn back burst credits
- T2/T3 unlimited: offer unlimited burst credit balance. If the instance used all its burst credits, we pay extra for the additional bursts

## AMIs

- Virtual machine images with customized set-up/software
- Custom AMI can provide the following advantages:
    - Pre-install packages
    - Faster boot time (no need to configure user data)
    - Machine comes pre-configured with monitoring
    - Security concerns - control over the machine in the network
    - Control of maintenance and updates of AMI over time
    - Active Directory Integration out of the box
    - Installing out app ahead of time
    - Using someone's AMI which is optimized for running an app (example: database)
- AMIs are not global, the are built for a specific region

### Using Public AMIs

- We can leverage AMIs from other people
- We can pay for other people's AMI by the hour
- AMIs can be found and published on the Amazon Marketplace

### AMI Storage

- AMI take space and they live in Amazon S3
- By default AMIs are private and locked for the account who created it
- We can make AMIs public and share them with other AWS accounts or sell them on the AMI Marketplace

### AMI Pricing

- AMIs live in S3, so we get charged for the actual space it takes in S3
- Overall it is quite inexpensive to store private AMIs
- We have to make sure to remove AMIs we don't use to save costs

### Cross Account AMI Copy

- It is possible the share AMI with another AWS account
- Sharing an AMI does not affect the ownership of the AMI
- If a shared AMI is copied, than the account who did the copy becomes the owner
- To copy an AMI that was shared from another account, the owner of the source AMI must grant read permissions for the storage that backs the AMI, either the associated EBS snapshot or an associated S3 bucket
- Limits:
    - An encrypted AMI can not be copied. Instead, if the underlying snapshot and encryption key where shared, we can copy the snapshot while re-encrypting it with a key of our own. The copied snapshot can be registered as a new AMI
    - We cant copy an AMI with an associated **billingProduct** code that was shared with us from another account. This includes Windows AMIs and AMIs from the AWS Marketplace. To copy a shared AMI with **billingProduct** code, we have to launch an EC2 instance from our account using the shared AMI and then create an AMI from source

## Elastic IPs

- When we stop and then start an EC2 instance, the public IP will change
- If we need to have a fixed public IP, we need an Elastic IP
- An Elastic IP is a public IPv4 IP we own as long as we don't delete it
- An Elastic IP can be attached to one instance at a time
- We can remap it across instances
- We don't pay for the Elastic IP if it is attached to a VM
- With an Elastic IP we can mask the failure of an instance or software by rapidly remapping the address to another instance in our account
- We can have up to 5 Elastic IPs in an account (soft limit, can be requested to be increased)
- Overall try we should try to avoid using Elastic IPs:
    - We could use a random public IP and register a DNS name to the instance
    - Use a LB with a static hostname

## CloudWatch Metrics for EC2

- **AWS Provided Metrics**:
    - Basic monitoring (default): metrics are collected at a 5 minute interval
    - Detailed Monitoring (paid): metrics are collected at a 1 minute interval
    - AWS Provided Metrics include: CPU, Network, Disk and Status Check metrics
- **Custom Metrics**:
    - Basic resolution of custom metrics is 1 minute
    - High resolution can go all the way up to 1 second
    - Custom metrics can include RAM, application level metrics
    - IAM permission is required on the EC2 instance role to be able to push metrics to CloudWatch

### EC2 Included Metrics

- CPU: CPU Utilization + Cred Usage / Balance (in case of T2/T3)
- Network: Network In/Out
- Status Checks:
    - Instance status: checks for the EC2 VM
    - System status: checks for the underlying hardware (Amazon health checks, no user control is granted over them)
- Disk (only for instance stored based instances): Read/Write for Ops/Bytes 
- **RAM is NOT included in AWS EC2 metrics!**

### CloudWatch Unified Agent

- New kind of metrics, gathers logs and metrics at the same time


