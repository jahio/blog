+++
author = "J. Austin Hughey"
date = 2020-06-11T07:55:52Z
description = ""
draft = false
slug = "new-relic-request-queueing-2013"
title = "New Relic Request Queueing (2013)"

+++


New Relic's "Request Queuing" metric is an often misunderstood and badly named figure that often confuses and worries application developers and DevOps engineers. In 2013, after receiving the umpteenth support request worried about this metric while working with [Engine Yard](http://www.engineyard.com/)'s Application Support team, I took some time and wrote this guide to show how request queueing is actually request _latency_, and does _not_ indicate that your application server has its request queue backed up, but instead that this is the difference in time between front-end proxy and the execution of the New Relic Agent code in your app.

[Understanding New Relic Queuing: Engine Yard Blog](https://www.engineyard.com/blog/new-relic-queuing) ([PDF](https://github.com/jahio/jahio.github.io/blob/master/assets/pdf/new-relic-request-queue.pdf))

(PDF is self-hosted in the event Engine Yard discontinues this article or their past blog posts.)

Request queuing in New Relic is often misinterpreted as "there's a bunch of unserved HTTP requests from users piled up in the (Puma/Thin/Unicorn/Passenger) queue! Oh no!". Thankfully - and somewhat confusingly - that's not the case at all. At Engine Yard, we added a custom header to [nginx](http://nginx.org/) that would mark the point in time when nginx itself received the HTTP request. This is after it's been routed from the user, across the internet, to the first entry point of an application (wherever your A or AAAA record sticks to), and through the load balancer (in our case, [haproxy](http://haproxy.com/)), and finally to nginx. Then, as the request was processed by the application server in use - whatever it may have been - New Relic's agent code in your application would look for this header and subtract the time difference between then, and the point in time it executed that code. Hence, this isn't a queue at all, it's _latency_.

The article goes into more detail and shows you how you can use various tools for both [Phusion Passenger](http://phusionpassenger.com/) and [Unicorn](http://unicorn.bogomips.org/) - the latter requiring some custom code that I've distilled into a tool - to find out how many requests are **ACTUALLY** piled up under the hood in the global request queue.

The resulting tool for checking Unicorn request queue (for real), [unicorn_status.rb](https://github.com/jahio/unicorn-status), was a joint effort between myself, [Adam Holt](https://github.com/omgitsads), and [Christopher Rigor](https://github.com/crigor).

People using [Phusion](http://phusion.nl/)'s excellent [Passenger](http://phusionpassenger.com/) application server can get this information much easier: simply run `passenger-status` on the VM that's processing your application's requests.

## How is request _latency_ useful?

If this figure gets unreasonably high (what's "unreasonable" depends on your application and architecture, but in general up to around ~50-100ms of latency is "normal") it could be a signal that something is getting between your reverse proxy (e.g. nginx) and your application's ability to process request code.

Here's a non-exhaustive summary list of possible causes when you're seeing high request **latency** in your application environment:

* Slow actions running as a "filter" before or around each request (e.g. `before_filter` in Rails);
* Database queries that aren't optimized or have no indexes - perhaps introducing a memory cache could help;
* Is the application server itself restarting or killing/respawning workers rather often? Check application server logs and system logs for memory consumption and `OOM` ("out of memory") events;
* Are there network-level requests to external resources that happen as part of the request? (Think microservices, etc.) Consider an out-of-band worker to fetch once and cache for ~5 minutes or whatever's appropriate;
* Is your application CPU intensive or running on underpowered hardware? Consider a vertical upgrade;
* Is the VM your application runs on (or the fleet of VMs) pushing swap? `free -m` will show you if swap is being used and if it is, free up some memory or upgrade total memory capacity;
* This is because, when a server needs and/or accesses swap, disk i/o - the speed at which a disk can read and/or write data - can be a huge bottleneck, even on solid state drives.
* Check your overall database performance; are indexes properly utilized (or are you missing any?) Database triggers happening unnecessarily? Is the DB server itself just plain hammered under load (e.g. BI reporting tools running)? Consider setting up a replica and conducting reads from it exclusively for things like reporting and/or long running processing tasks.

