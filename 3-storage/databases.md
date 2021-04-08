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