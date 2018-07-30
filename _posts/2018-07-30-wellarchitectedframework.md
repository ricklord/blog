---
layout: post
title: AWS Notes - Well-Architected Framework
image: "assets/images/banner-1686944_1920.jpg"
---
Notes on Amazon Web Services Well-Architected Framework

## AWS Well-Architected Framework Whitepapers
* []()

## Security
* Four areas of Security Pillar
    * Data protection
        * How are you encrypting and protecting data at rest?
        * How are you encrypting and protecting data in transit?
    * Privilege Management
        * How are you protecting access to and use of AWS root account credentials?
        * How are you defining roles and responsibilities of system users to control human access to the AWS management Console and APIs?
        * How are you limiting automated access to AWS resources?
        * How are you managing keys and credentials?        
    * Infrastructure protection
        * How are you enforcing network and host-level boundary protection?
        * How are you enforcing AWS service level protection?
        * How are you protecting the integrity of the OS on your EC2 instances?
    * Detective controls
        * How are you capturing and analyzing AWS logs?
* AWS NEVER initiates the movement of data between Regions. Content placed in a region will remain in that region unless the customer explicitly enables a feature or leverages a service that provides this functionality.

## Reliability
* Three areas of Reliability Pillar
    * Foundations
        * How are you managing AWS service limits for your account?
        * How are you planning your network topology on AWS?
        * Do you have an escalation path to deal with technical issues?
    * Change Management
        * How does your system adapt to changes in demand?
        * How are you monitoring AWS resources?
        * How are you executing change management?
    * Failure Management
        * How are you backing up your data?
        * How does your system withstand component failures?
        * How are you planning for recovery?

## Performance Efficiency
* Four areas of Performance Efficiency Pillar
    * Compute
        * How do you select the appropriate instance type for your system?
        * How do you ensure that you continue to have the most appropriate instance type as new instance type and features are introduced?
        * How do you monitor the instances post launch and ensure they are performing as expected?
        * How do you ensure that the quantity of your instances matches demand?
    * Storage
        * How do you select the appropriate storage solution for your system?
        * How do you ensure that you continue to have the most appropriate storage solution as new storage solutions and features are launched?
        * How do you monitor your storage solution to ensure it is performing as expected?
        * How do you ensure that the capacity and throughput of your storage solutions matches demand?
    * Database
        * How do you select the appropriate database solution for your system?
        * How do you ensure that you continue to have the most appropriate database solution as new storage solutions and features are launched?
        * How do you monitor your databases to ensure they are performing as expected?
        * How do you ensure that the capacity and throughput of your databases match demand?
    * Space-Time Trade-off
        * How do you select the proximity and caching solutions for your system?
        * How do you ensure that you continue to have the most appropriate proximity and caching solutions as new storage solutions and features are launched?
        * How do you monitor your proximity and caching solutions to ensure they are performing as expected?
        * How do you ensure that the capacity and throughput of your proximity and caching solutions match demand?

## Cost Optimization
* Four areas of the Cost Optimization Pillar
    * Matched supply and demand
        * How do you make sure your capacity matches but does not substantially exceed what you need?
        * How are you optimizing your usage of AWS services?
    * Cost-effective resources
        * Have you selected the appropriate pricing model to meet your cost targets?
        * Are there managed services (higher-level services than Amazon EC2, EBS, and S3) that you can use to improve your ROI?
    * Expenditure awareness
        * What access controls and procedures do you have in place to govern AWS costs?
        * How are you monitoring usage and spending?
        * How do you decommission resources that you no longer need, or stop resources that are temporarily not needed?
        * How do you consider data-transfer charges when designing your architecture?
    * Optimizing over time
        * How do you manage and/or consider the adoption of new services?

## Operational Excellence
* Three areas of Operational Excellence Pillar
    * Preparation
        * What best practices for cloud operations are you using?
        * How are you doing configuration management for your workload?
    * Operations
        * How are you evolving your workload while minimizing the impact of change?
        * How do you monitor the workload to ensure it is operating as expected?
    * Responses
        * How do you respond to unplanned operational events?
        * How is escalation managed when responding to unplanned operational events?
