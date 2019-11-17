---
layout: post
title:  Scalability
date:   2019-11-17 14:00:00 -0400
categories: 不周山
tag: framework
---


* content
{:toc}



# Part 1 - Clones

Public servers of a scalable web service are **hidden behind a load balancer**.  This load balancer **evenly distributes load (requests from your users) onto your group/cluster of  application servers**. That means that if, for example, user Steve interacts with your service, he may be served at his first request by server 2, then with his second request by server 9 and then maybe again by server 2 on his third request. 

Steve **should always get the same results of his request back**, independent what server he  “landed on”. That leads to the first golden rule for scalability: **_every server contains exactly the same codebase and does not store any user-related data, like sessions or profile pictures, on local disc or memory._** 

**Sessions need to be stored in a centralized data store which is accessible to all your application servers.** It can be an **external database or an external persistent cache**, like Redis. An external persistent cache will have better performance than an external database. By external I mean that the data store does not reside on the application servers. Instead, it is somewhere in or near the data center of your application servers. 

But what about deployment? How can you make sure that a code change is sent to all your servers without one server still serving old code? This tricky problem is fortunately already solved by the great tool Capistrano. It requires some learning, especially if you are not into Ruby on Rails, but it is definitely both the effort.

After “outsourcing” your sessions and serving the same codebase from all your servers, you can now create an image file from one of these servers (AWS calls this AMI - Amazon Machine Image.) **Use this AMI as a “super-clone” that all your new instances are based upon**. Whenever you start a new instance/clone, just do an initial deployment of your latest code and you are ready!