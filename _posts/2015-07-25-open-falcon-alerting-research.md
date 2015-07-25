---
layout: post
title: Open-Falcon Alerting Module Research
---

Alerting is an important part in monitoring system. The well-known open-source monitoring systems like ganglia, nagios, zabbix cannot fit the requirements of flexibility I want to have on alerting module.

### Requirements for Alerting

1. User can subscribe topic for alerts, rather than be pushed
2. Configurable threashold per user
2. Configurable threadhold based on multiple items
3. Time based alerting strategies per user
4. Alert Correlation, especially during alert flooding

[open-falcon](https://github.com/xiaomi/open-falcon) is an open-source monitoring and alerting system written in Go language. It provides scalability on each sub-modules, including agent, receiver, judge (to send alarm or not), alarm,  sender, dashboard etc. open-falcon mainly uses redis as queue and cache to build the scalable system. Luckily, I found out open-falcon can fit some of these requirements easily.

The configurable threadhold and time based strategies have already been built on the system in [judge module](https://github.com/open-falcon/judge).

However, the alert correlation is not a easy one. open-falcon has a simple implementation based on priorities. It means only combine the high priority alerts will never been merged. And for low priority alerts happened in one minute, the job will push them into an alert table in MySQL database, with related information, then send user only a link to the combined messages on a webapp. This implementation can be found in [alarm module](https://github.com/open-falcon/alarm).

This priority based combining strategy can reduce some flooding. But there are still some problems:

1. 1 minute is a short and fixed window for combining. Is it a proper time window and should provide more flexibility? Is there any practice on this?
2. Priority cannot fit on all scenarios. Different dimesion is needed, like alert tag (type) and CMDB based combining:
	1. Server
	2. Cluster
	3. Racket
	4. IDC

## Summary

open-falcon is a very scalable framework. It can provide enough flexibility on alerting strategies. But for alert correlation, not good enough. There are some papers on alert correlation. I need to do deeper research and revisit this topic later.