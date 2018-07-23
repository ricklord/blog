---
layout: post
title: AWS Notes - Elastic Compute (EC2)
image: "assets/images/banner-1686944_1920.jpg"
---
## EC2 Basics
* EC2 stands for "Elastic Compute Cloud"
* AWS has about 15 regions, not including GovCloud
* Within each region are a minimum of 2 Availability Zones (AZs)
* An AZ is akin to a data center. It is an abstraction of locally physically
isolated facility with its own power, cooling, etc.
* IAM Users and policy documents are global, and can be accessed across all regions

### Block Storage
* Lower end instance types no longer support instance/ephemeral storage, only
EBS based volumes
* Instance storage only exists for lifetime of instance and can't be detached and
re-attached to another instance. Use instance storage for faster disk access to
temporary disk-based like caches, buffers, temporary files, etc
* EBS is Elastic Block Storage. EBS volumes can live beyond lifetime of attached
instance
* EBS volumes must always exist in the same AZ as the instance they are attached to
* EBS volumes are placed in a single, specific AZ, but are replicated across
multiple physical disks to prevent against single component failure
* All block storage volume types EXCEPT for standard Magnetic can be modified
without downtime. You can change the EBS volume size and storage type
* To move an EBS volume with data to another AZ, you must take a snapshot and
then do Create volume from that. You should suspend writes to the source volume
first before the snapshot
* To move an EC2 instance to another region:
    * Take a snapshot of the instance storage
    * Copy the snapshot to another region
    * Create an AMI from the snapshot
    * Create new instance in other region, using custom image
* You can create an AMI from either directly from the EBS-backed instance or
from a snapshot of the instance
* Volumes exist on EBS
* Snapshots exist on S3
* Snapshots are point in time copies
* Snapshots are incremental
* First snapshot takes time to create
* Snapshots of encrypted volumes are encrypted automatically
* Volumes restored from encrypted snapshots are encrypted automatically
* Snapshots can be shared with other accounts, but ONLY if they are unencrypted
* Snapshots can be shared with specific AWS accounts or made public

#### EBS Disk Types
* General Purpose SSD (GP2)
    * 3 IOPS/GB
    * Up to 10,000 IOPS
    * Burst up to 3000 IOPS for extended time on 3TB+ size volumes
* Provisioned IOPS SSD (IO1)
    * Designed for I/O intensive applications
    * Use if you need more than 10,000 IOPS
    * Can provision up to 20,00 IOPS per volume
* Throughput Optimized HDD (ST1)
    * Cannot be a boot volume
    * Used for big Data, data warehouses, log processing
* Cold HDD (SC1)
    * Cannot be a boot volume
    * Lowest cost for infrequent accessed workloads
    * Used for file servers
* Magnetic (Standard)
    * Legacy, but still usable
    * Lowest cost per GB of all storage types
    * Infrequent accessed workloads

#### RAID
* RAID = Redundant Array of Independent Disks
    * RAID 0 - Striped, No Redundancy, Good Performance
    * RAID 1 - Mirrored, Redundancy
    * RAID 5 - 3 or more disks, Good for reads, bad for writes. AWS does NOT
        recommend ever using RAID 5 on EBS
    * RAID 10 - Striped and Mirrored, Good redundancy, good performance    
* When snapshotting a RAID array, need to be a little more careful to stop all
writes and take an application consistent snapshot.
    * Stop application from writing to disk
    * Flush all caches to disk
    * This can be done either by freezing the filesystem, unmounting the RAID array, or just shutting down the associated EC2 instance

### AMIs
* All AMIs are categorized as either backed by EBS or backed by instance store
* EBS based root volumes are created from an EBS snapshot
* Instance store based root volumes are created from a template in S3
* Instance store also called ephemeral storage
* Instance store volumes can't be stopped, only rebooted or terminated. If underlying host fails, you lose your data.
* EBS backed instances can be stopped
* Both instance store instances and EBS instances can be rebooted
* By default, both types of root volumes will be deleted upon termination, but you have the option to keep EBS based root volumes    

### Pricing Options
* On Demand
    * Fixed rate by the hour
    * No upfront cost
    * Applications with short term, spiky or unpredictable work loads
    * Initial application development / testing
* Reserved
    * Capacity reservation with 1 or 3 year terms
    * Upfront payments, overall discounted from the on demand hourly charge
    * Steady state or predictable workloads
    * Standard RIs - Up to 75% off on-demand pricing
    * Convertable RIs - Up to 54% off on-demand pricing. Convertible refers to
      being able to change to higher-end compute instances
    * Scheduled RIs - Reserved instances for a specific. predictable, recurring
     schedule
* Spot
    * Bid for capacity, can get cheaper if you have flexibility on running
workloads at non-peak demand times
    * Applications with flexible start and end times
    * Applications only feasible at very low compute prices
    * If spot instance terminated by Amazon, you won't be charged for partial hour
    * If you terminate sport instance, you'll be charged the full hour
* Dedicated hosts
    * Dedicated physical servers
    * Can save you money if you have existing server bound software licenses
    that you want to keep using
    * For when there are regulatory requirements that prevent you from using
    multi-tenant virtualization
    * Can be purchased on-demand
    * Can be purchased as reserved instances for up to 70% off on-demand cost

### EC2 Instance Types
* F1 - Field Programmable Gate array. Used for genomics research, financial
analytics, real-time video processing
* I3 - High speed storage. Used for NoSQL DBs, data warehousing
* G3 - Graphics intensive. Used for video encoding, 3D applications
* H1 - High Disk Throughput. Used for MapReduce workloads and distributed file
systems (e.g. HDFS)
* T2 - Lowest cost, General Purpose. Used for Web servers, small databases
* D2 - Dense Storage. Used for file servers and data warehousing
* R4 - Memory Optimized. Used for memory intensive applications and databases
* M5 - General Purpose. Used for app servers
* C5 - Compute Optimized. Used for CPU intensive applications
* P3 - Graphics/General Purpose GPU. Used for machine learning, bit coin mining
* X1 - Memory Optimized. Used for applications require extreme amounts of memory,
e.g. Apache Spark, SAP HANA

### Security Groups
* A security group is a virtual firewall
* All inbound traffic blocked by default
* All outbound traffic allowed
* Instances can be associated with multiple security groups
* Specify rules for protocol, port, and source
* SGs are stateful, meaning any traffic allowed in is also allowed back out, as
opposed to NACLs in which you have to make sure return traffic has a rule to get out
* Security group rules apply immediately to any instances currently using that SG
* Security group rules are "ALLOW" rules.  Everything is denied by default.
* You can't block specific IPs with SGs. Need NACLs for that

### Creating EC2 Instances
* On Linux AMIs, root user is ec2-user
* After you download a key pair to use for SSHing to Linux instances, you must
change permissions on the pem to give only owner read permissions, otherwise the EC2 instance won't let you connect with it, and it will warn you that the file permissions are too open
* To admin a Linux EC2 instance, Use the key pair specified at launch to SSH to
the public IP: SSH -i mykey.pem ec2-user@<public IP>
* Security group needs to allow SSH traffic on port 22 for Linux
* Security group needs to allow RDP traffic on port 3389 for Windows
* EC2 instance and any EBS volumes being attached to it need to be in the same
AZ
* curl http://169.254.169.254/latest/meta-data to get instance metadata
* curl http://169.254.169.254/latest/user-data to get bootstrap user data

### EC2 Placement Groups
* Two types of placement groups
    * Cluster Placement Group
        * A grouping of instances within a single AZ
        * Used for applications that need low network latency, high network throughput, or both
    * Spread Placement Group
        * A grouping of instances that are each placed on distinct underlying hardware
        * Used for applications that have a small number of critical instances that should be kept separate from each other
* Spread placement groups are newer than clustered, so if exams doesn't specify, assume they're talking about clustered
* Placement group names must be unique within your AWS account
* Only certain instances can be launched in to a clustered placement group. No low end server types, only compute optimized, GPU, memory optimized, storage optimized
* AWS recommends homongeneous instances within placement groups
* You can't merge placement groups
* You can't move existing instances into placement groups
* You can create an AMI from an existing instance and then launch a new instance from the AMI into the placement group

### Elastic File System (EFS)
* Block based storage
* Centralized file server
* Can use file and directory level permissions
* Supports Network File System version 4 (NSFv4) protocol
* You only pay for the storage you use, no pre-provisioning required
* Can scale up to Petabytes
* Can support thousands of concurrent NFS connections
* Data is stored across multiple AZ's within a region
* Read after Write consistency


### Load Balancers
* 3 Types
    * Application Load Balancers - Layer 7 LBs
    * Network Load Balancers - Extreme performance of Layer 4 (TCP)
    * Classic Load Balancers - Legacy Elastic Load balancers, can do both Layer 7 and Layer 4 LB features, but Layer 7 more restricted, can only do sticky sessions
* HTTP 504 means your LB can't communicate with the backing instance(s), timing out either due to connectivity or too long running request.
* Requests to instance from LB use the X-Forwarded-For header to pass origin IP of your end user
* Instances monitored by ELB are reported as InService or OutOfService
* ELBs use health check requests to determine whether instance is healthy
* ELBs have their own DNS name. AWS does NOT give you IP of ELBs

### CloudWatch
* Dashboards
* Alarms allow you to get notified when particular thresholds are met
* Events - allow you to respond to state changes in your AWS resources
* Logs - aggregate, monitor, and store logs

### Lambda
* Lambda scales out (not up) automatically
* Lambda functions are independent, 1 event = 1 function
* Lambda is serverless (from developer perspective)
* Lambda functions can trigger other lambda functions
* Architectures can get complicated, AWS X-Ray allows you to debug what is happening
* Lambda can do things globally, e.g. you can use it to back up S3 buckets to other S3 buckets

* Supported Runtimes
    * .NET Core
    * Go
    * Java
    * Node.js
    * Python

* Lambda Trigger Types
    * API Gateway
    * AWS IoT
    * Alexa Skills Kit
    * Alexa Smart Home
    * CloudFront
    * CloudWatch Events
    * CloudWatch Logs
    * CodeCommit
    * Cognito Sync Trigger
    * DynamoDB
    * Kinesis
    * S3
    * SNS
    * SQS

* Lambda Pricing
    * Number of requests
        * First M requests/month free
        * $0.20/M requests thereafter
    * Duration  
        * Calculated from time your code begins executing until it returns or terminates
        * Rounded to nearest 100ms
        * Price depends on the amount of memory you allocate to your function. You are charged $0.00001667 / Gb-sec used
