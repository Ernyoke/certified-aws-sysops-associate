# EC2 Storage and Data Management - EBS and EFS

## EBS Volumes

- An EC2 machine loses its root volume when it is manually terminated
- Unexpected terminations might happen from time to time (AWS would send an email)
- Sometimes, we need a way to store instance date somewhere
- An EBS (Elastic Block Store) Volume is a network drive we attach to our EC2 instances while they are running
- Data can be persisted on the EBS volumes
- EBS is a network drive:
    - It uses network to communicate to the instance, which means there might be a bit of latency
    - It can be detached from an EC2 and attached to another instance
- It is locked to an AZ, in order to move them to another AZ, we need to create a snapshot
- EBS volumes have provisioned capacity (size in GB and IOPS). We get billed for all the provisioned capacity
- The capacity of the drive can be increased over time

## EBS Volume Types

### GP2

- Recommended for most workloads
- It can be a system boot volume
- Size can be 1GiB to 16GiB
- Small GP2 volumes can burst IOPS to 3000
- Max IOPS is 16000
- We get 3 IOPS per GB, meaning at 5334GiB we are at the max IOPS capacity

### IO1

- Recommended for critical business applications which require sustained IOPS performance or more than 16000 IOPS per volume
- Recommended for database workloads
- Size can range between 4GiB and 16TiB
- IOPS is provisioned: min 100 - max 64000 (Nitro instances), 32000 (other instances)
- Maximum ratio of provisioned IOPS to requested volume size is 50:1

### ST1

- Recommended for streaming workloads requiring consistent, fast throughput at low price
- Can not be a boot volume
- Size ranges from 500 Gib to 16 TiB
- Max IOPS is 500
- Maximum throughput is 500 MiB/s - with ability to burst

### SC1

- Throughput-oriented storage for large volumes of data that is infrequently accessed
- Recommended for scenarios where the lowest storage cost is important
- Can not be a boot volume
- Size ranges from 500 Gib to 16 TiB
- Max IOPS is 250
- Maximum throughput is 250 MiB/s - with ability to burst

### GP2 Volume I/O Burst

- If the GP2 volume is less than 1000GiB (means IOPS is less than 3000), it can burst to 3000 IOPS performance
- Concept is similar to t2 instances with CPU bursts
- We accumulate burst credit over time, which allows the volume to have good performance when needed
- The bigger the volume, the faster we can fill up the burst credit balance
- If the I/O credit balance is empty:
    - The max I/O we get becomes the baseline we paid
    - If we see the balance being 0 all the time, we have to increase the GP2 volume or switch to IO1
    - We can use CloudWatch to monitor the I/O credit balance
- Burst is also available for ST1 and SC1

## EBS Computing Throughput

- GP2:
    - Throughput in MB/s = (Volume Size in GiB) * (IOPS per GiB) * (I/O size in KiB)
    - Example: 3000 I/O operations per second * 256 KiB per I/O  operation = 75 MiB/s
    - Limit to a max of 250 MiB/s (means volume >= 334 GiB wont increase throughput)
- IO1:
    - Throughput in MB/s = (Provisioned IOPS) * (I/O size in KiB)
    - The throughput limit of IO1 volumes is 256 KiB/s for each IOPS provisioned
    - Limit to a max of 500 MiB/s (at 32000 IOPS) and 1000 MiB/s (at 64000 IOPS)

## EBS Volume Resizing

- We can only increase the EBS volumes size and provisioned IOPS
- After resizing a volume, we need to repartition the drive
- After increasing the size, it is possible for the volume to be for a longer time in an optimization phase. The volume will be usable during this time

## EBS Snapshots

- Snapshots are incremental: only backup changed blocks
- EBS backups use IO and we should not run them while our application is handling a lot of traffic
- Snapshots are stored in S3, but we are not directly able to see them
- The is not necessary required for a volume to be detached while the snapshot is being made, but it is recommended
- An account can have max 100.000 snapshots
- We can copy snapshots across AZs or regions
- We can make an AMI from a snapshot
- EBS volumes restored from a snapshot need to be pre-warmed (using `fio` or `dd` commands to read the entire volume)
- Snapshots can be automated using Amazon Data Lifecycle Manager

## EBS Migrations

- EBS volumes are locked to a specific AZ
- To migrate a volume to a different AZ or region, we have to do the following:
    - Create a snapshot from the volume
    - (optional) Copy the snapshot to a different region
    - Create a volume from the snapshot in the AZ of choice

## EBS Encryption

- We we create an encrypted EBS volume, we get the following:
    - Data at rest is encrypted inside the volume
    - All the data in flight moving between the instance and the volume is encrypted
    - All the snapshots are encrypted
    - All volumes created from snapshot are encrypted
- Encryption and decryption are handled transparently
- Encryption has a minimal impact on latency
- EBS Encryption leverages keys from KMS (AES-256)
- Copying an un-encrypted snapshot allows encryptions
- Snapshot of encrypted volumes are also encrypted

### Encrypt an Un-encrypted EBS volume

- Create an EBS snapshot from the volume
- Encrypt the EBS snapshot (using volume copy)
- Create a new EBS volume from the snapshot

## EBS Volume vs Instance Store

- Some instances do not come with root EBS volumes, instead they come with an instance store
- Instance store = ephemeral storage: data is lost on stop or termination
- An instance store is a physically attached drive to the machine, while EBS is a network drive
- Instance store is block storage just like EBS
- Pros of Instance Store:
    - Better I/O performance
    - Good for buffer, cache, scratch data and temporary content
    - Data on an instance stores survives an instance reboot
- Cons:
    - On stop or termination of an instance, the instance stor lost
    - An instance store can not be resized
    - Backups from instance store data must be operated by the user

## Local EC2 Instance Store

- Instance store is a physical disk attached to the physical server where the EC2 runs
- Instance store provides very high IOPS
- Instance store disks are up to 7.5 TiB, stripped to reach 30 TiB
- It can not be resized
- Risk of data loss if hardware fails

## EBS for SysOps

- If we plan to use the root volume of an instance after it is terminated, we have to set the "Delete on Termination" flag to "No"
- If we use EBS for high performance, we should use EBS-optimized instance types
- If an EBS volume is unused, we still pay for it
- For cost saving over a long period of time, we should create a snapshot from a volume and restore it later (3x cost saving)
- In case of high wait time or slow response increase IOPS
- EC2 wont start with EBS volume as root: make sure volume names are properly mapped (/dev/xvdb instead of /dev/xvda for example)
- After increasing a volume size, we still need to repartition to use the incremental storage

## EBS RAID Configuration

- EBS is already redundant storage (replicated within az AZ)
- RAID is possible as long as OS supports it
- Some RAID options are:
    - RAID 0
    - RAID 1
    - RAID 5, 6 (not recommended of EBS)

### RAID 0

- Used for increase performance
- RAID 0 means combining 2 or more volumes and getting the total disk space and I/O
- If one dist fails, all the data is compromised
- Use case for RAID 0:
    - An application that needs a lot of IOPS and does not need fault tolerance
    - A database which has replication already built in

### RAID 1

- Used for increase fault tolerance
- RAID 1 means mirroring a volume to another
- If one disk fails, our logical volume is still working
- We have to send the data to two EBS volumes at the same time, meaning we need EC2 instances with higher network capability

## CloudWatch and EBS

- Important EBS volume CloudWatch metrics:
    - **VolumeIdleTime**: number of seconds when no read/write is submitted
    - **VolumeQueueLength**: number of operations waiting ti be executed. Higher number means probably an IOPS or application issue
    - **BurstBalance**: if it becomes 0, we need a volume with more IOPS
- GP2 volume reporting interval: 5 minutes
- IO1 volume reporting interval: 1 minute
- Reporting intervals are unchangeable
- EBS volumes have status checks:
    - OK - the volume is performing goog
    - Warning - performance is bellow expected
    - Impaired -  stalled, performance is severly degraded
    - Insufficient data - metric data collection in progress

## EFS - Elastic File System Overview

- Managed NFS (network file system) that can be mounted on many EC2
- EFS works with EC2 instances in multi-AZ
- Highly available, scalable, expensive (3x GP2), pay per use
- Use cases: content management, web service, data sharing, WordPress
- Uses NFSv4.1 protocol
- Uses security group to control access to EFS
- **EFS is compatible with Linux based AMIs (not windows)!**
- Provides encryption at rest using KMS
- It has a POSIX file system with a standard file API

## EFS Performance and Storage Classes

- EFS Scale:
    - Thousands of concurrent NFS clients 10 GB+/s throughput
    - It can grow to PB scale network file system
- Performance mode (set at EFS creation time):
    - General purpose (default): latency-sensitive use cases (web server, CMS, etc..)
    - Max I/O: higher latency, throughput, highly parallel (big data, media processing)
- Storage tiers with lifecycle management feature:
    - Standard: for frequently accessed files
    - Infrequent access (EFS-IA): cost to retrieve files, lower price to store