---
layout: post
title: Quartz Job Not Recovering after Tomcat Restart
---

[Quartz](https://quartz-scheduler.org/) server can be configured clustered to have multiple servers run the same set of cron jobs. It's good for load balancing and failover. Each quartz job can be set an option 'request recovery', which means whenever the job crashed (due to server down, application server down etc), the job can be recovered by other alive instances in the cluster.

Here is the instruction to configure cluster for quartz:

[http://quartz-scheduler.org/documentation/quartz-2.x/configuration/ConfigJDBCJobStoreClustering](http://quartz-scheduler.org/documentation/quartz-2.x/configuration/ConfigJDBCJobStoreClustering)

However, in production, I met a problem that nearly every time restarting tomcat in the cluster, some of the jobs hang in "BLOCKED" state, and never come back again. It shows the jobs are FIRED, but none of the instance detect it's dead. It doesn't make sense as it should be the scenario which 'recovery' should cover.

I found something catalina.out when shutting down tomcat:

    Oct 08, 2015 7:50:00 PM org.apache.catalina.loader.WebappClassLoader loadClass
    INFO: Illegal access: this web application instance has been stopped already.  Could not load com.xxx.cron.job.SSHExecutorJob.  The eventual following stack trace is caused by an error thrown for debugging purposes as well as to attempt to terminate the thread which caused the illegal access, and has no functional impact.
    java.lang.IllegalStateException
    at org.apache.catalina.loader.WebappClassLoader.loadClass(WebappClassLoader.java:1612)
    at org.apache.catalina.loader.WebappClassLoader.loadClass(WebappClassLoader.java:1571)
    at org.springframework.scheduling.quartz.ResourceLoaderClassLoadHelper.loadClass(ResourceLoaderClassLoadHelper.java:78)
    at org.springframework.scheduling.quartz.ResourceLoaderClassLoadHelper.loadClass(ResourceLoaderClassLoadHelper.java:83)
    at org.quartz.impl.jdbcjobstore.StdJDBCDelegate.selectJobDetail(StdJDBCDelegate.java:852)
    at org.quartz.impl.jdbcjobstore.JobStoreSupport.retrieveJob(JobStoreSupport.java:1385)
    at org.quartz.impl.jdbcjobstore.JobStoreSupport.triggerFired(JobStoreSupport.java:2964)
    at org.quartz.impl.jdbcjobstore.JobStoreSupport$43.execute(JobStoreSupport.java:2908)
    at org.quartz.impl.jdbcjobstore.JobStoreSupport$43.execute(JobStoreSupport.java:2901)
    at org.quartz.impl.jdbcjobstore.JobStoreSupport.executeInNonManagedTXLock(JobStoreSupport.java:3787)
    at org.quartz.impl.jdbcjobstore.JobStoreSupport.triggersFired(JobStoreSupport.java:2900)
    at org.quartz.core.QuartzSchedulerThread.run(QuartzSchedulerThread.java:336)

### Solution

To fix this problem, I add the following java option in tomcat startup script:

    -Dorg.apache.catalina.loader.WebappClassLoader.ENABLE_CLEAR_REFERENCES=false

In tomcat doc, it says:

> If true, Tomcat attempts to null out any static or final fields from loaded classes when a web application is stopped as a work around for apparent garbage collection bugs and application coding errors.
> There have been some issues reported with log4j when this option is true.
> Applications without memory leaks using recent JVMs should operate correctly with this option set to false.
> If not specified, the default value of true will be used.

It's a common problem in tomcat, which may impacts log4j and some of other third party libraries. We can either fix the code or add the option above. I haven't found side effect so far.

### Reference

1. [http://m.blog.csdn.net/blog/wgw335363240/7263442](http://m.blog.csdn.net/blog/wgw335363240/7263442)
2. [http://stackoverflow.com/questions/6564553/why-does-tomcat-throw-java-lang-illegalstateexception-class-invariant-violatio](http://stackoverflow.com/questions/6564553/why-does-tomcat-throw-java-lang-illegalstateexception-class-invariant-violatio)
3. [https://issues.liferay.com/browse/LPS-14189](https://issues.liferay.com/browse/LPS-14189)
4. [https://tomcat.apache.org/tomcat-6.0-doc/config/systemprops.html](https://tomcat.apache.org/tomcat-6.0-doc/config/systemprops.html)
