---
layout: post
title: AWS Notes - Application Integration Services
image: "assets/images/banner-1686944_1920.jpg"
---
Notes on Amazon Web Services SQS, SWF, SNS, Elastic Transcode, API Gateway, Kinesis

## AWS Application Integration FAQs
* [Amazon SQS FAQ](https://aws.amazon.com/sqs/faqs/)
* [Amazon SNS FAQ](https://aws.amazon.com/sns/faqs/)
* [Amazon SWF FAQ](https://aws.amazon.com/swf/faqs/)
* [Amazon API Gateway FAQ](https://aws.amazon.com/api-gateway/faqs/)
* [Amazon Kinesis FAQ](https://aws.amazon.com/kinesis/streams/faqs/)
* [Amazon MQ FAQ](https://aws.amazon.com/amazon-mq/features/#FAQs)
* [AWS AppSync FAQ](https://aws.amazon.com/appsync/product-details/#FAQs)
* [AWS Step Functions FAQ](https://aws.amazon.com/step-functions/faqs/)

## Simple Queuing Service (SQS)
* Messages can contain up to 256 KB of text in any format
* SQS is pull based
* Messages can be kept in the queue from 1 minute to 14 days
* Default retention period is 4 days
* SQS Visibility Timeout
    * The amount of time that the message is invisible in the SQS queue after a reader dequeues a message. Provided the job is processed before the visibility time out expires, the message will then be deleted from the queue. If the job is not processed within that time, the message will become visible again and another reader will process it.  This could result in the same message being delivered twice
    * Default visibility timeout is 30 seconds
    * Increase it if your task takes > 30 seconds
    * Maximum visibility timeout is 12 hours
* Two different queue types
    * Standard queues
        * Nearly unlimited # TPS
        * Guaranteed "at least once" message delivery
        * More than one copy of a message might be delivered
        * No order guarantee.  Generally will try to deliver in order on best effort, but can't depend on delivery order
    * FIFO queues
        * Guaranteed delivered in order
        * Guaranteed deliver once processing
        * Messages remain in queue until consumer processes and deletes it
        * Supports message groups
        * Limited to 300 TPS
* SQS Long Polling
    * Default mechanism is short polling. In short polling, the consumer call to SQS returns immediately even if the queue being polled is empty
    * With long polling, SQS doesn't return response until a message arrives in the queue or the poll times out
    * Long polling can save money because SQS charges per call

## Simple Workflow Service (SWF)
* For coordinated tasks
* tasks represent invocations of various processing steps in an application which could be executable code, web service calls, human actions, and scripts
* Used in Amazon.com for order fulfillment
* 3 Types of actors
    * SWF Workflow Starters - applications that can initiate a workflow
    * SWF Workers - programs that interact with SWF to get tasks, process received tasks, and return results
    * SWF Deciders - programs that control the coordination of tasks (i.e. ordering, concurrency, scheduling) according to the business rules
* SWF stores tasks, assigns them to workers when they are ready, and monitors progress
* SWF ensures a task is assigned only once and never duplicated (in contrast to standard SQS queues)
* SWF Domains are scoping containers for all workflow, activity types, and execution
* SWF presents a task oriented API, whereas SQS offers a message oriented API

## Simple Notification Service (SNS)
* SNS is for push notifications
* Allows subscriptions through Topics
* When an SNS topic is created, an ARN is created for the topic
* Types of SNS Subscribers
    * Push direct to mobile
    * Push to SMS
    * Push to e-mail / e-mail JSON
    * Push direct to SQS queues
    * Push to HTTP and HTTPS endpoints
    * Push to Lambda functions. When publishing a message to an SNS topic that has a Lambda function subscribed to it, the Lambda is invoked with the payload of the message
* All messages to SNS are stored redundantly across multiple AZs
* Pricing
    * User pays $0.50 per million Amazon SNS requests
    * $0.60 per 100,000 HTTP notifications
    * $0.75 per 100 SMS notifications
    * $2.00 per 100,000 e-mail notifications

## Elastic Transcoder
* Priced based on number of minutes spent transcoding and the resolution at which you transcode
* Provides presets for transcoding to popular output formats so you don't need to guess at what formats work best for specific device types

## API Gateway
* API Caching enables you to configure a TTL for responses
* Low cost and Scales easily
* Ability to throttle requests to prevent attacks
* Connect to CloudWatch to log all requests
* If you are using Javascript/AJAX calls that use multiple domains with API Gateway, ensure that you have enabled CORS on API Gateway, or you will see the  "Origin policy cannot be read at the remote resource" error

## Kinesis
* Load and analyze streaming data


### Kinesis Streams
* Producers and consumers
* Producers write to shards
* Consumers read from shards
* A stream consists of multiple shards
* Can retain data in a shard up to 7 days
* Default retention 24 hours
* 5 TPS for reads, up to a maximum total read rate of 2 MB per second
* Up to 1,000 records per second for writes, up to a maximum total data write rate of 1 MB per second (including partition keys)
* Data capacity of stream is a function of the number of shards that you specify for the stream
* Total capacity of the stream is the sum of the capacities of its shards

### Kinesis Firehose
* Don't have to set up shards
* Data passes through and can be queried by Lambda functions on the way through
* Can send to RedShift by way of S3
* Can send to Elasticsearch

### Kinesis Analytics
* Allows you to run SQL queries on data within Firehose or Streams and send to S3, RedShit, or ElasticSearch
