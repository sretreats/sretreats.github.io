---
title: Linux know how, that must be known by SREs
date: 2021-03-01
hero: "/images/posts/linux-know-how-sres.jpg"
excerpt: Linux know how, that must be known by SREs
authors:
  - Hemant Negi
  - Hdev
---


## Was your Mac rebooted recently?


```bash
> uptime
```
   
The response also shows whether CPU utilization is increasing recently:

6:35  up 9 days, 11:56, 7 users, load averages: 2.09 2.24 2.06
   
“load averages” numbers are calculations of the average system load over three periods of times: the last one-, five-, and fifteen-minute periods. These count the number of processes either using or waiting for CPU (the ready queue or run queue) increments the load number by 1.
In a system with four CPU cores, a load average of 3.73 would indicate that there were, on average, 3.73 processes ready to run, and each one could be scheduled into a CPU.



There are some test suits that neeeds to be definer



Hello world this is a change!
