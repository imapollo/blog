---
layout: post
title: Metrics as a Service?
---

More and more teams go with data-driven IT operations, for its rapid feedback cycle and effective iteration. Metrics, which can collect and display data in different forms, is becoming more and more important. If we want to provide Metrics as a Service, what do we need?

### Features

1. Multiple approaches to collect data from different data source, including agent, client and API.
2. Show data in different dimension, like by hostname, location, service, project. We can get these inforamtion from upstream system like CMDB.
3. Analysis data, trending; Detect issues.
4. Javascript (JQuery) plugin, maybe combined with echart, to easily show data just by providing data sources.

### Architecture

Most of the companys uses open-source monitoring system to collect data from the beginning, like Nagios, Ganglia, Zabbix. We uses Zabbix at the moment, but found some limitations for Zabbix to scale up.

In [Zabbix 2.4.0 release notes](https://www.zabbix.com/documentation/2.4/manual/introduction/whatsnew240), it says "Node-based distributed monitoring removed", which means only Proxy mode distributed monitoring is recommended in Zabbix. In that approach, only one master is configured, and the backend database is still one big bottleneck.

#### Workaround Approaches

1. Build up self proxy service to distribute the requests.
2. Other open-source monitoring system with NoSQL DB, like open-falcon, OpenTSDB.

For #1, we can provide a DNS like service to distribute requests to different Zabbix servers. We are intend to using location based distribution so that we don't need to setup proxies for each DC again. However, if we still want to scale up in one location, what can we do? How can we re-balance the items and hosts in different Zabbix servers again?

For #2, open-falcon is a good monitoring framework. All of the components can be separated and scaled up. It uses RRDTool as backend server (?). It also consider cross data center issues by implementing [Gateway](https://github.com/open-falcon/gateway).

We can also considering using other Time-Series database like OpenTSDB as the backend storage, but we need to implement the framework / components on it.

### Reference

1. http://net.doit.wisc.edu/~dwcarder/rrdcache/