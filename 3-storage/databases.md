# Databases

## AWS RDS Overview

- RDS = Relational Database Service
- It is a managed DB service for databases using SQL as a query language
- It allows to create databases in the cloud that are managed by AWS
- It has support for the following databases:
    - PostgreSQL
    - Oracle
    - MySQL
    - MariaDB
    - Microsoft SQL
    - Aurora
- AWS RDS is a managed service, which means using we can get:
    - Automatic OS patching
    - Continuous backups and restore points to specific timestamps (Point in Time Restore)
    - Monitoring and dashboards
    - Read replicas for improved read performance
    - Multi AZ setup for disaster recovery
    - Maintenance window for updates and upgrades
    - Scaling capabilities (vertical and horizontal)
- We can't SSH into underlying OS in case of managed RDS!

## RDS Read Replicas

- Read Replicas: DB replicas used for reading only to improve performance
- We can have up to 5 read replicas
- Read replicas can be within the same AZ, cross AZ or cross region
- Replication is happening asynchronously, reads can be eventually consistent
- Replicas can be promoted to their own database
- Applications must update the connection string to leverage read replicas
- A read replica can be promoted as a standalone database
- Each replica has it's one DNS endpoint
- Read replicas are not supported by Oracle!

## RDS Multi AZ

- Used for disaster recovery
- The replication is synchronous to one DB instance in a different AZ (standby instance)
- One DNS name is exposed to the application which has an automatic failover
- Failover can happen in case of loss of AZ, network outage, instance storage failure
- The application is writing in a single DB, multi AZ can not be used for scaling
- Multi AZ standby instance does not support reads
- The failover happens only in case of the following conditions:
    - The primary database instance fails
    - There is an AZ outage
    - The DB instance server type is changed
    - The operating system of the DB instance is undergoing software patching
    - A manual failover of the DB instance was initiated using Reboot with failover
- There are no failovers for DB operation: long-running queries, deadlocks or database corruption errors
- Endpoint is the same after failover, meaning there is no URL change needed in the application using the database
- Lower maintenance impact is happening on the standby instance which is promoted to master
- Backups are created for standby instance
- Multi AZ is not cross region, is just cross availability zone

## RDS Backups

- Backups are automatically enabled in RDS
- Automated backups:
    - Daily full snapshot of the database
    - Capture transaction logs in real time => ability to restore to any point in time
    - Automated backups are retained for 7 days (can be increased to 35 days)
- DB Snapshots:
    - Manually triggered by the user
    - Retention pf a backup is as long as we want

## RDS Encryption

- RDS provides encryption at rest with AWS KMS - AES-256 encryption
- We can use SSL certificates to encrypt data to RDS in flight
- To enforce SSL:
    - PostgreSQL: `rds.force_ssl=1` in the AWS RDS console (Parameter Groups)
    - MySQL: `GRANT USAGE ON *.* TO 'user'@'%' REQUiRE SSL;`
- To connect using SSL:
    - We have to provide the SSL Trust certificate
    - We have to provide SSL options when connection to the database

## RDS Security

- RDS databases are usually deployed within a private subnet
- RDS security works by leveraging security groups -  they control who can communicate with RDS
- IAM policies help control whe can manage AWS RDS
- Traditional username and password can be used to log into a database
- IAM users can now also bu used (for MySQL/Aurora only)
- Encryption at rest:
    - Can only be enabled when we first create the DB instance
    - Alternatively we can take a snapshot from an unencrypted DB and enable encryption when restoring
- Our responsibility:
    - Check the ports/IP/security group inbound riles in the database's SG
    - In-database user creation is up to the user
    - Creating a database with or without public access
    - Ensure parameters groups or DB is configured to only allow SSL connections
- AWS responsibility:
    - No SSH access
    - No manual database patching
    - No manual OS patching
    - No way to audit the underlying instance

## DB Parameters Groups

- We can configure the DB engine using parameters groups
- Dynamic parameters are applied immediately
- Static parameters are applied after instance reboot
- We can modify parameter groups associated with a DB (reboot will be required)
- Must known parameters:
    - PostgreSQL/SQL server: `rds.force.ssl=1` - force SSL connections

## Backups and Snapshots

- Backups:
    - Backups are continuous and allow point in time recovery
    - Backups happen during maintenance window
    - When we delete a database instance, we can retain the backups
    - Backups have a retention period between 0 and 35 days
- Snapshots:
    - Snapshots takes IO operations and can stop the database from seconds to minutes
    - Snapshots taken on Multi AZ DB don't impact the master - just the standby
    - Snapshots are incremental after the first snapshot
    - We can copy and share DB snapshots
    - Manual snapshots don't expire
    - We can take a final snapshot when we delete a database

## RDS API

- `DescribeDBInstances API`:
    - Helps to get a list of all the DB instances we have deployed including read replicas
    - Helps to get the database version
- `CreateDBSnapshot API`:
    - Make a snapshot from a DB
- `DescribeEvents API`:
    - Helps to return information about events related to our DB instance
- `RebootDBInstance API`:
    - Helps to initiate a forced failover by rebooting a DB instance

## RDS with CloudWatch

- CloudWatch metrics associated with RDS (gathered from the hypervisor):
    - DatabaseConnections
    - SwapUsage
    - ReadIOPS/WriteIOPS
    - ReadLatency/WriteLatency
    - ReadThroughput/WriteThroughput
    - DiskQueueDept
    - FreeStorageSpace
- Enhanced Monitoring (gathered from an agent on the DB instance):
    - Useful when we need to see how different processes or threads use the CPU
    - Access to over 50 new CPU, memory, file system and disk I/O metrics

## Performance Insights

- Allows to visualize database performance and analyze any issues that affect it
- With the Performance Insights dashboard we can visualize the database and load and filter them by:
    - By Waits: find the resource which is the bottleneck (CPU, IO, lock, etc.)
    - By SQL statements: find the SQL statement that is problematic
    - By Hosts: find the server which is using the most the database
    - By User: find the user who is using the most our database
- DBLoad: the number of active sessions for the DB engine
- We can view the SQL queries that are putting load on the database

## Aurora Overview

- Aurora is a proprietary technology from AWS
- PostgreSQL and MySQL are both supported as Aurora DB, meaning that the DB connection drivers will work as if Aurora was a PostgreSQL or MySQL database
- Aurora is "AWS cloud optimized" and claims 5x performance improvements over MySQL or RDS, over 3x performance of PostgreSQL or RDS
- Aurora storage automatically grows in increments of 10GB up to 64TB
- Aurora can have 15 replicas while MySQL can have 5, and the replication process is faster (sub 10 ms replica lag)
- Failover in Aurora is instantaneous. It is HA native
- Aurora costs more than RDS (around 20%)

## Aurora HA and Read Scaling

- Aurora stores 6 copies across 3 AZ:
    - 4 copies out of 6 needed for writes
    - 3 copies our of 6 needed for reads
    - Self healing with peer-to-peer replications
    - Storage is stripped 100s of volumes
- One Aurora instance takes writes
- Automate failover for master is less than 30 seconds
- Master + up to 15 Aurora Read Replicas serve reads
- Supports cross region replication (CRR)

## Aurora DB Cluster

- Writer Endpoint: pointing to master
- Auto Scaling: always have the right number of read replicas
- Reader Endpoint: helps with connection load balancing, connects to all read replicas

## Features of Aurora

- Automatic failover
- Backup and recovery
- Isolation and security
- Industry compliance
- Push-button scaling
- Automated patching with zero downtime
- Advanced monitoring
- Routine maintenance
- Backtrack: restore data at any point of time without using backups

## Aurora Security

- Encryption at rest using KMS
- Automated backups, snapshots and replicas are also encrypted
- Encryption if flight using SSL (same process as MySQL or PostgreSQL)
- Authentication using IAM
- We are responsible for protecting the instance with security groups

## Aurora Serverless

- No need to choose an instance size
- Helpful when we can't predict workloads
- DB cluster starts, shutdown and scales automatically based on CPU/connections
- We can migrate from Aurora Cluster to Aurora Serverless and vice versa
- Aurora Serverless is measured in ACU (Aurora Capacity Units)
- Billing is happening in 5 minute increments of ACU

## ElastiCache

- ElastiCache is a managed Redis os Memcached instance
- Caches are in-memory databases with really high performance and low latency
- Caches help reduce the load from the databases for read intensive workloads
- They can store session state
- ElastiCache provides write scaling using sharding an read scaling using read replicas
- It has Multi AZ with failover
- AWS takes care of OS maintenance, patching, optimization, setup, configuration, monitoring, failure recovery and backups

## ElastiCache: Redis vs Memcached

- Redis:
    - Multi AZ with auto-failover
    - Read replicas can be used to scale reads and have high availability
    - Data Durability can be enabled using AOF persistance
    - Backups can be restored
- Memcached:
    - Multi-node for partition of data (sharding)
    - No persistance
    - No backup and restore
    - Multi-threaded architecture