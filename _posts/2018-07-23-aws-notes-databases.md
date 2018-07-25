---
layout: post
title: AWS Notes - Databases
image: "assets/images/banner-1686944_1920.jpg"
---
Notes on Amazon Web Services RDS, Aurora, DynamoDB, RedShift, Elasticache

## Supported DB Types
* RDS - RDBMSs for OLTP
    * SQL Server
    * Oracle
    * MySQL
    * PostgreSQL
    * Aurora - Two flavors, MySQL and PostgreSQL
    * MariaDB
* DynamoDB - NoSQL
* RedShift - OLAP
* Elasticache - In Memory Caching

## RDS
* AWS gives a DNS name, not a public IP for connecting to your RDS instance
* If unable to connect, make sure your RDS security group is opened up to connections to port 3306 from the security group containing client instance

### RDS Backups
* When restoring backups either from AB or SB Snapshot, the restored DB will be a new RDS instance with a new FQDN
* Automated Backups
    * Allow you to recover your DB to any point within a retention period
    * Retention period 1-35 days
    * Takes a full daily snapshot and store transaction logs throughout day
    * Recovery starts with most recent snapshot and applies transaction logs
    * Automated backups enabled by default
    * Backups stored in S3
    * AWS gives your backup storage space equal to size of your DB
    * Backups taken during defined window
    * During backup window, storage I/O may be suspended and you may experience higher latency
    * Automated backups are deleted when RDS instance is deleted
* Database Snapshots
    * User initiated, manual
    * Persist even after original RDS instance is deleted

### RDS Encryption
* Encryption at rest currently supported for all RDS engines
* Encryption done using AWS Key Management Service
* Once RDS instance is encrypted, the data stored at rest in underlying storage is encrypted, as are automated backups, read replicas, and snapshots
* Encrypting an EXISTING RDS DB instance is NOT supported.  Instead, you need to take a DB snapshot, make a copy of the DB snapshot with Encryption enabled on the copy, and then restore from the encrypted DB snapshot

### Multi-AZ
* DB in one AZ is synchronously replicated to an instance in another AZ
* Multi-AZ is for DR only, not performance.  Because it's synchronous replication, writes are actually slower in this configuration
* Can't use the secondary instance as an independent read node
* AWS automatically switches the RDS FQDN for the instance to point to the second instance in the event of a failure of primary
* Supported for all the RDS engines, although it's transparent in the case of Aurora because that by default is spread across multiple AZs

### Read Replicas
* Improve performance by offloading reads to replicated copies
* Uses asynchronous replication
* By default can have 5 read replicas per production RDS instance
* Read replicas can replicate to other read replicas
* Read replicas can exist in another AZ or even in another region
* Read replicas are NOT available for MS SQL Server or Oracle
* Must have automatic backups turned on in order to deploy read replicas
* Each read replica has its own FQDN
* You can have read replicas that are Multi-AZ
* You can also create read replicas of Multi-AZ DBs
* Read replicas can be promoted to their own DB, but this will break replication
* You can have read replica in another region

## DynamoDB
* Scales easily, Push-button scaling, as opposed to RDS which may involve some downtime in order to scale up
* Supports document and key-value data models
* Stored on SSD
* Spread across 3 geographically distinct data centers
* Different Consistency Models available
    * Eventually Consistent Reads
    * Strongly Consistent Reads
* Provisioned Throughput Capacity
    * Write Throughput $0.0065 per hour for every 10 units
    * Read Throughput $0.0065 per hour for every 50 units
* Storage costs $0.25 / GB per month
* Cost efficient for applications with high read to write ratio

## RedShift
* Petabyte scale data warehouse service
* $0.25 per hour, scale up to 1PiB or more for $1K/TiB/year
* Costs less than 1/10th most other commercial solutions
* For OLAP applications
* Data warehouse databases use different type of DB and infrastructure architecture
* Column-based data layout
* Single Node (up to 160GiB)
* Multi-Node
    * Leader Node - Manages client connections and receives queries
    * Compute Nodes
        * Store data and perform queries and computations
        * Up to 128 compute nodes
* Advanced compression - Typically columnar data can compress better because data of similar type and therefore possible duplicate values are stored contiguously. RedShift samples data and selects the most appropriate compression scheme
* More space efficient because in addition to compression, it doesn't require indexes or materialized views
* Easy to add compute nodes, RedShift automatically distributes data and queries across compute nodes
* Pricing
    * Compute Node Hours - Total number of hours across all compute nodes.  Billed 1 unit per node per hour, so a 3-node RedShift cluster running full-time for an entire month would incur 2160 instance hours (3 nodes * 24 hours * 30 days)
    * Backup - Charged for backup
    * Data Transfer - Charged for transfer within a VPC, not outside of it
* RedShift Security
    * Encrypted in transit using SSL
    * Encrypted at rest using AES-256
    * By default RedShift takes care of key management
    * You can opt to manage your own keys through Hardware Security Modules (HSM) or through AWS KMS
* Availability
    * RedShift only available in 1 Availability Zone
    * It's for business intelligence, not transaction processing
    * Can restore snapshots to new AZs in the event of an outage

## Elasticache
* Two open source in-memory engines supported
    * Memcached
        * Elasticache is protocol compliant with memcached
        * Does not support Multi-AZ
    * Redis
        * in-memory key-value store that supports sorted sets and lists
        * Supports Master/Slave replication and Multi-AZ which can be used to achieve cross AZ redundancy

## Aurora
* Amazon's own RDBMS engine
* MySQL-Compatible
* 5x better performance than MySQL
* Pricing 1/10th commercial DBs
* Not available in all regions
* Minimum instance size is R3 large
* Data can be encrypted using KMS managed keys

### Aurora Scaling
* Start with 10GB
* Scales in 10GB increments to 64TB with autoscaling
* Compute resources can scale up to 32vCPUs and 244 GiB of memory
* 6 copies of data - 2 copies of data stored in each AZ, with minimum of 3 AZs used
* Designed to transparently handle loss of up to 2 copies of data without affecting DB write availability and up to 3 copies without affecting read availability
* Self-healing - Aurora data blocks are actively scanned and repaired
* Replicas, 2 types
    * Aurora replicas (currently up to 15)
        * Automatic failover
        * Tier number of replica determines which replica takes over if master fails
    * MySQL Read Replicas (currently up to 5) - No automatic failover
