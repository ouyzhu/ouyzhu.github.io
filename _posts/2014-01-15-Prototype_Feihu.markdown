---
layout: post
title:  "服务质量可视化"
date:   2014-01-15 12:25:20
categories: jekyll update
---

#服务质量可视化

##背景

项目中需要对业务的各种质量指标进行监控，如接口的成功率，平均响应时间等。在实践中，经历过以下的一些做法：

1. 每日扫描和统计日志文件。好处是执行简单，一个脚本加一个定时器便可以搞定。问题是不够实时，不容易及时发现问题。多机汇总也不太方便，变通的做法是把日志传送到一台机再处理。
2. 业务系统实时（或准实时）上报相关日志信息到一台机，集中汇总和统计。好处是实时性提高，但从头开发的话，工作量并不小。
3. 使用一些成熟的开源框架，目前相关的各个环节已经有不少成熟的框架。本文不打算对关这些框架进行比较，仅介绍自己的一种实践。

##系统结构

<img src="{{ site.url }}/images/Prototype_Feihu_Architecture.png" alt= "系统结构" style="width: 635px;"/>

* 上报端（Shipper）
  承担日志的分析和本地汇总工作，然后上报结果数据，以避免对汇集端（Collector）的网络和存储产生过大的压力。主要包括[Logstash]/Java/[Statsd]/[Nodejs]。
* 汇集端（Collector）
  负责记录来自各个上报端（Shipper）的上报，并负责展示。主要包括[Graphite]/Python/Apache。
* 日志（log）
  业务系统负责*输出*环节，日志可以是容器或反向代理（如Apache/Tomcat/Nginx等）的access日志，也可以是业务代码通过日志系统（如SLF4J等）输出的有规则的日志。
* [Logstash]
  负责采集日志，分析并上报到本地的[Statsd]，它会监视配置好的日志，一旦有增加即会进行分析和上报。
* [Statsd]
  负责接收本地[Logstash]的上报，在本地进行汇总，按设定的间隔时间（默认10秒）上报到汇集端[Graphite]的上报监听接口（carbon）
* [Carbon/Whisper/Graphite-Web]
  Carbon负责接收多台机器的上报，将信息按时间片存于whisper中，Graphite Web端用于展示数据

##系统搭建

写了简单的搭建脚本，请参考[这里](http://github.com/ouyzhu/feihu)

##引用与参考

* [Logstash](http://logstash.net/)
* [Stated](https://github.com/etsy/statsd/)
* [Nodejs](http://nodejs.org/)
* [Graphite](http://graphite.wikidot.com/)
* [来自Flicker的经验](http://code.flickr.net/2008/10/27/counting-timing/)
* [其它人的经验](http://matt.aimonetti.net/posts/2013/06/26/practical-guide-to-graphite-monitoring/)


