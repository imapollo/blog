---
layout: post
title: LUIGI and EOM
---

One of my colleagues in DevOps team creates a tool named EOM in my previous company. It's basically an orchestration framework to pipeline the tasks needed in development environments provisioning. It handles failures, cocurrently run multiple tasks. However, the framework was only used inside the DevOps team, for this specific task. There should be broader scenarios for the framework to be used.

[LUIGI](http://luigi.readthedocs.org/en/latest/index.html) is a Spotify python based pipeline framework. It handles dependency resolution, workflow management, visualization, handling failures, command line integration, and much more. That's a lot of extensions on the idea, especially tasks for Big Data stuffs like mr, hive, hdfs, etc.

To use the pipeline, we need to write some python code. We need to extend 2 basic classes ```luigi.WrapperTask``` & ```luigi.Task```. Override ```run()``` method, and defines dependencies between different tasks with ```requires()```, then it's done.

There are some examples in:

[https://github.com/spotify/luigi/tree/master/examples](https://github.com/spotify/luigi/tree/master/examples)

If you have tasks need to be put into pipeline, please check LUIGI around. It's quite straightforward to be implemented.