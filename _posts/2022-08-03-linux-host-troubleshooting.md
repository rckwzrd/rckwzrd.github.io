---
layout: post
---

The text [DevOps for the Desperate](https://www.goodreads.com/book/show/59879642-devops-for-the-desperate) covers a range of scenarios for troubleshooting issues on a remote linux host.

Below are notes on each scenario and associated tools and protocols. Adminstrative permissions, `sudo` rights, and `ssh` access are assumed. 

## How to troubleshoot

1. Start simple
2. Build mental model
3. Develop a theory
4. Use consistent tools
5. Keep notes
6. Ask for help


##  High load average

The linux metric `load average` indicates how busy a host is. CPU usage and disk I/O is used to compute the metric. If a host is in an impaired state load average may be a factor.

To troubleshoot first examine load average and then identify processes contributing to a high load.
As a *general* rule if load average is greater than CPU core count there may be stalled processes impacting performance.

### uptime

The `uptime` command displays how long the host has been running, number of users, and 1/5/15 minute load averages. Note the difference in load times to infer if the host is expierncing a high average load over time. If load is greater than CPU core count, continue investigating.

### top

The `top` command provides information about processes running on the host. It provides CPU, memory, and process information. This tool refreshes itself every 3.0 seconds, so let it run for several cycles and note the differences between values.

Key `top` columns are:
1. `PID`
2. `RES`
3. `%CPU`
4. `%MEM`
5. `COMMAND`

If a process `PID` has high `%CPU` or `%MEM` it may be contributing to high load averages. The `COMMAND` field indicates the name of the process and can be used as a starting point to investigate the further.

##  High memory usage

### free

### vmstat

### ps

##  High iowait
##  Host name resolution failure
##  Out of disk space
##  Connection refused
##  Searching logs
##  Probing processes
