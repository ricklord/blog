---
layout: post
title: AWS Notes - Route 53
image: "assets/images/banner-1686944_1920.jpg"
---
Notes on Amazon Web Services Route 53 DNS Service

## Route 53 Basics
* Route 53 is a global service
* AWS supports IPv6 in Route63 and VPC
* Need Alias record to map zone apex to ELB because CNAMEs don't support zone apex and ELB only ever gives DNS, not an IP
* Route 53 charges for CNAME resolution request, but not for Alias record
* DNS record types
    * SOA (Start of Authority)
        * Name of server that supplied the data for the zone
        * administrator of the zone
        * Current version of the data file
        * Number of seconds a secondary name server should wait before checking for updates
        * Number of seconds a secondary name server should wait before retrying a failed zone transfer
        * Maximum number of seconds a secondary name server can use data before it must either refresh or expire
        * Default number of seconds for the TTL file on resource records
    * NS (Name server) - Used by top level domain servers to direct traffic to the content DNS server which contains the authoritative DNS records
    * A (Address) - Translates a name to an IP address
    * CNAME (Canonical Name)
        * Resolve one name to another.
        * Can't use with for zone apex
    * Alias record - Like CNAME, but can be used to map a zone apex record
* You never get an IP address for an ELB

## Route 53 Routing Policies
* Simple
    * Default
    * Round robin
* Weighted
    * Weight routing between multiple regions or between different ELBs in the same region
    * Weighting happens over longer period of time throughout day, not at level of seconds or minutes
    * Requires a record (usually Alias or A) for each target and specify a number representing weight, but that number is a percent.  All numbers for same domain are added and then the number for a particular record divided by total to figure the weighting
    * Good for if you want to test new site features on a certain percentage of users
* Latency
    * Route traffic based on lowest network latency for your end user
    * When creating records for latency routing, have to specify the region, even though AWS should know region for AWS targets
* Failover
    * Used for active/passive
    * A healthcheck monitors health of endpoints
    * Requires setup of healthcheck
    * Healthcheck should be specifically for the primary region target
    * Must specify either primary or secondary on each record
    * Failover record doesn't need to specify a healthcheck
* Geolocation
    * Chooses where to route traffic based on geographic location of users
    * Can specify targets based on continents, countries, or even by state for US users
