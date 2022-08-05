---
layout: post
---

The text [DevOps for the Desperate](https://www.goodreads.com/book/show/59879642-devops-for-the-desperate) covers a range of scenarios for troubleshooting issues on a remote linux host.

Below are notes on each scenario and associated tools and protocols. Basic `cli` knowledge, `sudo` rights, and `ssh` access are assumed. 

## How to troubleshoot

A mental approach:

1. Start simple
2. Build mental model
3. Develop a theory
4. Use consistent tools
5. Keep notes
6. Ask for help


##  High load average

The linux metric `load average` indicates how busy a host is. CPU usage and disk IO is used to compute the metric. If a host is in an impaired state load average may be a factor.

To troubleshoot first examine load average and then identify processes contributing to a high load.
As a general rule if load average is greater than CPU core count there may be stalled processes impacting performance.

### uptime

The `uptime` command displays how long the host has been running, number of users, and 1/5/15 minute load averages. Note the difference in load times to infer if the host is expierncing a high average load over time. If load is greater than CPU core count, continue investigating.

### top

The `top` command provides information about processes running on the host. It provides CPU, memory, and process information. This tool refreshes itself every 3.0 seconds, so let it run for several cycles and note the differences between values.

If a process `PID` has high `%CPU` or `%MEM` it may be contributing to high load averages. The `COMMAND` field indicates the name of the process and can be used as a starting point to investigate the further.


##  High memory usage

Spikes in traffic, memory leaks, or failing applications can cause memory to be consumed at high rates. By design, linux allocates all memory to cache and buffers also making free memory appear low.

The first step is to confirm that the host is really running low on memory or if the kernel is simply swapping cached and buffered memory between processes. Then move to identify the memory consuming processes and handle them.

### free

The `free -hm` command displays free and used system memory at the time it is run. The `-hm` flag outputs memory usage in a human readable format. The `mem:` row indicates actual RAM usages while the `swap:` row is related to memory written to disk. 

If the `free` column in the `swap` row is low the host is writing memory to the disk and running slow. The `used` and `free` columns can be misleading. Reference the `available` column to get a feel for how much memory is actually available for new processes. 

### vmstat

The `vmstat 1 5` command provides information about processes, memory, IO, disks, and CPU state. The `1 5` arguements will set `vmstat` to poll the host for information 5 times every minute. This makes memory trends easier to spot. 

The first row of data in the report is system average since boot. The `memory` sections provides information on memory moving between `free`, `buff`, and `cache`. The `swap` section shows memory being paged in and out of the disk. Low relative free memory and lots of swap activity indicate that the host consuming high rates of free memory and relying swapped disk memory.

The `r` column indicates the number of processes waiting to run while the `b` column indicates the number of processes sleeping. High count in `r` indicates a possible CPU bottleneck. High count in `b` indicates that the host is waiting on disk or network I/O.

### ps

The `ps -efly --sort=-rss | head` command provides a snapshot of all running processes and memory usage. The `efly --sort=-rss | head` flag sorts the processes by highest memory usage and shows the top ten results. The `CMD` column in the output shows the name of each process. The `RSS` column gives the amount of memory being used by the process in kilobytes.


##  High iowait

A host has high `iowait` when it is spending too much time waiting for disk IO. This metric is measured by tracking the percentage of time a CPU is idle while waiting for IO disk request. High `iowait` creates higher average load and CPU usage. Intense application read and writing or slow network storage can be the root cause.

A small amount of `iowait` is normal on a modern system. The challenge is differentiating normal `iowait` with sustained high `iowait` over a period. After identifying high `iowait` move to finding the process responsible.

### iostat

The `iostat -xz 1 20` command reports IO and CPU stats for storage devices mounted to the host. The flag `-xz 1 20` polls the system 20 times every second and returns an extended statistic format. The `%iowait` column will show what percent of time the CPU is waiting on disk requests. The `w/s` column indicates the number of writes per second hitting a disk and the `util` column indicates disk utilization. 

Reviewing polling results for a period of time should help identify if sustained high `iowait` is affecting the host.

### iotop

The `sudo iotop -oPab` command displays IO usage relative to processes on the host. It is similiar to `top`. The flag `-oPab` will constantly poll the host and return cummulative IO stats. Elevated permissions are required to run `iotop`. The `IO` column will show IO usage and the `PID` and `COMMAND` columns can be used to identify process.

Reviewing the polling results will help identify what process or proccesses are creating high `iowait`.

##  Out of disk space
##  Connection refused
##  Searching logs
##  Probing processes
