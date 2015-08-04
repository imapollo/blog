---
layout: post
title: Cattle Powering Rancher
---

[Rancher](http://rancher.com/) is an open source software platform that implements a purpose-built infrastructure for running Docker containers in production. As in production, there are requirements to create new requirements in infrastructure services such as networking, storage, load balancer, security, service discovery, and resource management.

## Cattle

[Cattle](https://github.com/rancher/cattle) is the orchestration engine that powers Rancher. Its primary role is meta data management and orchestration of external systems. Cattle doesn't do real work, but instead delegate to other components (agents) to do the actual work.

## Cattle Components

Following are the key components in Cattle:

1. Process Engine
2. Configuration Management (meta-data)
3. Resources & Agents

### Process Engine

Cattle defines a [process engine](https://github.com/rancher/rancher/wiki/How-Cattle-works%3A-Process-handling) which define and control the actions and state for different resources. It also control and log the transitioning between different states. The engine also has an option to define the process as idempotent. If the option is turned on, the process will be run twice. the engine will check if the first and second result is the same, and then throw exception if it's not.

### Configuration Management (meta-data)

Cattle uses Netflix open-source configuration management framework [Archaius](https://github.com/Netflix/archaius) for its own meta data managment. The Key feature of Archaius is that it change properties dynamically at runtime. It can read from JDBC, file, zookeeper etc.

### Resources & Agents

Cattle does no real job, but instead delegates to the agents. There are some pre-defined resources and processes in cattle framework, such as HA, load balancer, IP, vnet, NIC, storage etc. It provides steady middle layer for different tasks on container orchestration.
