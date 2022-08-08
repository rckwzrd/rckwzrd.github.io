---
layout: post
---

The text [DevOps for the Desperate](https://www.goodreads.com/book/show/59879642-devops-for-the-desperate) covers a range of scenarios for troubleshooting issues on a remote linux host.

Below are notes on each scenario and the tools used to investigate. Basic `cli` knowledge, `sudo` rights, and `ssh` access are assumed. 


## How to troubleshoot

Before troubleshooting can begin it is important to have a framework to guide the investigation.

The following approach can be used for methodical troubleshooting:

1. Start simple
2. Build mental model
3. Develop a theory
4. Use consistent tools
5. Keep notes
6. Ask for help


## High load average

The linux metric `load average` indicates how busy a host is. CPU usage and disk IO is used to compute the metric. If a host is in an impaired state load average may be a factor.

To troubleshoot first examine load average and then identify processes contributing to a high load.
As a general rule if load average is greater than CPU core count there may be stalled processes impacting performance.

### uptime

The `uptime` command displays how long the host has been running, number of users, and 1/5/15 minute load averages. Note the difference in load times to infer if the host is expierncing a high average load over time. If load is greater than CPU core count, continue investigating.

```bash
mlr@pop-os:~$ uptime
 19:28:23 up 16 min,  1 user,  load average: 0.25, 0.43, 0.36
```

### top

The `top` command provides information about processes running on the host. It provides CPU, memory, and process information. This tool refreshes itself every 3.0 seconds, so let it run for several cycles and note the differences between values.

If a process `PID` has high `%CPU` or `%MEM` it may be contributing to high load averages. The `COMMAND` field indicates the name of the process and can be used as a starting point to investigate the further.

```bash
mlr@pop-os:~$ top
top - 19:33:09 up 21 min,  1 user,  load average: 0.33, 0.34, 0.34
Tasks: 295 total,   1 running, 294 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.1 us,  0.4 sy,  0.0 ni, 99.5 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   7663.1 total,   3067.7 free,   2129.3 used,   2466.2 buff/cache
MiB Swap:   4095.5 total,   4095.5 free,      0.0 used.   4625.7 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                                
    249 root     -51   0       0      0      0 S   2.3   0.0   0:16.12 irq/156-DLL0945:00                                     
   1413 root      15  -5 1386604 114068  69684 S   2.0   1.5   0:32.99 Xorg                                                   
   2261 mlr       15  -5  635864  54248  40084 S   1.3   0.7   0:08.50 gnome-terminal-                                        
   1614 mlr       15  -5 5357140 265588 118724 S   1.0   3.4   0:32.49 gnome-shell   
```

##  High memory usage

Spikes in traffic, memory leaks, or failing applications can cause memory to be consumed at high rates. By design, linux allocates all memory to cache and buffers also making free memory appear low.

The first step is to confirm that the host is really running low on memory or if the kernel is simply swapping cached and buffered memory between processes. Then move to identify the memory consuming processes and handle them.

### free

The `free -hm` command displays free and used system memory at the time it is run. The `-hm` flag outputs memory usage in a human readable format. The `mem:` row indicates actual RAM usages while the `swap:` row is related to memory written to disk. 

If the `free` column in the `swap` row is low the host is writing memory to the disk and running slow. The `used` and `free` columns can be misleading. Reference the `available` column to get a feel for how much memory is actually available for new processes.

```bash
free -hm
               total        used        free      shared  buff/cache   available
Mem:           7.5Gi       2.1Gi       3.0Gi       643Mi       2.4Gi       4.5Gi
Swap:          4.0Gi          0B       4.0Gi
```

### vmstat

The `vmstat 1 5` command provides information about processes, memory, IO, disks, and CPU state. The `1 5` arguements will set `vmstat` to poll the host for information 5 times every minute. This makes memory trends easier to spot. 

The first row of data in the report is system average since boot. The `memory` sections provides information on memory moving between `free`, `buff`, and `cache`. The `swap` section shows memory being paged in and out of the disk. Low relative free memory and lots of swap activity indicate that the host consuming high rates of free memory and relying swapped disk memory.

The `r` column indicates the number of processes waiting to run while the `b` column indicates the number of processes sleeping. High count in `r` indicates a possible CPU bottleneck. High count in `b` indicates that the host is waiting on disk or network I/O.

```bash
mlr@pop-os:~$ vmstat 1 5
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 3092416 107712 2437708    0    0   137    72  342  468  2  1 97  0  0
 0  0      0 3085652 107712 2443952    0    0     0    96 1309 1748  1  0 99  0  0
 0  0      0 3085400 107712 2441900    0    0     0     0 2976 3101  1  1 99  0  0
 0  0      0 3096512 107712 2439596    0    0     0     0 3068 2655  1  1 99  0  0
 0  0      0 3096260 107712 2439660    0    0     0   112  303  540  0  0 100  0  0

```

### ps

The `ps -efly --sort=-rss | head` command provides a snapshot of all running processes and memory usage. The `efly --sort=-rss | head` flag sorts the processes by highest memory usage and shows the top ten results. The `CMD` column in the output shows the name of each process. The `RSS` column gives the amount of memory being used by the process in kilobytes.

```bash
ps -efly --sort=-rss | head
S UID          PID    PPID  C PRI  NI   RSS    SZ WCHAN  STIME TTY          TIME CMD
S mlr         1807    1578  0  85   5 630440 345151 do_pol 19:12 ?      00:00:08 io.elementary.appcenter -s
S mlr         1614    1316  2  75  -5 265828 1347579 do_pol 19:12 ?     00:00:41 /usr/bin/gnome-shell
```

## High iowait

A host has high `iowait` when it is spending too much time waiting for disk IO. This metric is measured by tracking the percentage of time a CPU is idle while waiting for IO disk request. High `iowait` creates higher average load and CPU usage. Intense application read and writing or slow network storage can be the root cause.

A small amount of `iowait` is normal on a modern system. The challenge is differentiating normal `iowait` with sustained high `iowait` over a period. After identifying high `iowait` move to finding the process responsible.


### iostat

The `iostat -xz 1 20` command reports IO and CPU stats for storage devices mounted to the host. The flag `-xz 1 20` polls the system 20 times every second and returns an extended statistic format. The `%iowait` column will show what percent of time the CPU is waiting on disk requests. The `w/s` column indicates the number of writes per second hitting a disk and the `util` column indicates disk utilization. 

Reviewing polling results for a period of time should help identify if sustained high `iowait` is affecting the host.

```bash
mlr@pop-os:~$ iostat -xz 1 20
Linux 5.17.5-76051705-generic (pop-os) 	08/07/2022 	_x86_64_	(8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           1.86    0.23    0.85    0.14    0.00   96.93

Device            r/s     rkB/s   rrqm/s  %rrqm r_await rareq-sz     w/s     wkB/s   wrqm/s  %wrqm w_await wareq-sz     d/s     dkB/s   drqm/s  %drqm d_await dareq-sz     f/s f_await  aqu-sz  %util
dm-0             0.10      2.63     0.00   0.00    0.16    25.65    0.01      0.00     0.00   0.00    0.00     0.44    0.00      0.00     0.00   0.00    0.00     0.00    0.00    0.00    0.00   0.00
nvme0n1         25.17    938.55    15.53  38.15    0.22    37.28   21.51    520.71    15.15  41.32    1.54    24.20    0.00      0.00     0.00   0.00    0.00     0.00    0.89    0.52    0.04   1.84

```

### iotop

The `sudo iotop -oPab` command displays IO usage relative to processes on the host. It is similiar to `top`. The flag `-oPab` will constantly poll the host and return cummulative IO stats. Elevated permissions are required to run `iotop`. The `IO` column will show IO usage and the `PID` and `COMMAND` columns can be used to identify process.

Reviewing the polling results will help identify what process or proccesses are creating high `iowait`.

```bash
mlr@pop-os:~$ sudo iotop -oPab
Total DISK READ:         0.00 B/s | Total DISK WRITE:       443.33 K/s
Current DISK READ:       0.00 B/s | Current DISK WRITE:     391.87 K/s
    PID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN      IO    COMMAND
    352 be/3 root          0.00 B    364.00 K  ?unavailable?  [jbd2/nvme0n1p3-8]
    405 be/4 root          0.00 B     64.00 K  ?unavailable?  systemd-journald
   1077 be/4 root          0.00 B     20.00 K  ?unavailable?  packagekitd
```

## Out of disk space

At some point a host will run out of disk space. This can be caused by an application, accumulated logs, or build up of files. The drive and file system with low disk space needs to be identified first. Then the isolated drive can be searched to locate the files consuming large amounts of disk space.

### df

The `df -h` command displays disk usage on all mounted filesystems. The flag `-h` returns a human readable output. Review the `size`, `used`, and `use%` columns to evaluate what disks under `filesystem` are close to capacity.

```bash
mlr@pop-os:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           767M  1.9M  765M   1% /run
/dev/nvme0n1p3  226G   33G  182G  16% /
tmpfs           3.8G     0  3.8G   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
/dev/nvme0n1p1  497M  362M  136M  73% /boot/efi
/dev/nvme0n1p2  4.0G  2.6G  1.5G  64% /recovery
tmpfs           767M  180K  767M   1% /run/user/1000
```

### find

The `sudo find / -type f -size +100M -exec du -ah {} + | sort -hr | head` command searches a specified portion of the filesystem for directories and files that match a criteria. In this case the entire command searches the root filesystem for all files greater than 100mb, sorts by size, and then displays the top ten largest files. Elevated permissions are required. Evaluating the output will provide large files to review and a link to the processes filling the disk.

```bash
mlr@pop-os:~$ sudo find / -type f -size +100M -exec du -ah {} + | sort -hr | head
2.6G	/var/cache/pop-upgrade/recovery.iso
2.4G	/recovery/casper-1FE5-33A5/filesystem.squashfs
666M	/home/mlr/Desktop/export/apple_health_export/export.xml
```

## Connection refused

When a host is impaired its internal APIs may refuse connections over a network. When inspecting application logs a `connection refused` error over a port may be observed. Troubleshooting will involve checking network status to and from the host.

### curl

The `curl` command is used to check if another webserver is responding to requests. This will help confirm if the impaired host is completely down for all users. If the internal API is impaired a `connection refused` or `connection timeout` may be returned. This implies the message packet is getting dropped at the host port or firewall.

```bash
mlr@pop-os:~$ curl google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>

```

### ss

The `sudo ss -l -n -p | grep 4000` command will dump socket information on a host. It can be used to check if the API is actually listening on a port. The flag `-l -n -p` pulls all listening sockets, does not resolve `HTTP`/`SSH`, and reports the process using the process. The output is piped to `grep` to search for the desired port. Elevated permissions are required to see all processes. 

```bash
mlr@pop-os:~$ sudo ss -l -n -p | grep 4000
tcp   LISTEN 0   4096   127.0.0.1:4000    0.0.0.0:*    users:(("bundle",pid=5253,fd=8))                                                            
```

### tcpdump

The `sudo tcpdump -ni any tcp port 4000` command can be used capture network traffic on a host. This will verify if traffic is reaching the impaired host. Executing the command will begin capturing and inspecting `tcp` packets recieved on all interfaces. The flag `-ni any tcp` stops `dns` resolution and tells `tcpdump` to listen for traffic from `8080`. Elevated permissions required. Reviewing the flags in the output will show if connections are being refused. If repeated `[S]` and `[R]` flags are observed this implies that a remote `IP` is attempting to sync with the host but the connection is being reset.

```bash
mlr@pop-os:~$ sudo tcpdump -ni any tcp port 4000
tcpdump: data link type LINUX_SLL2
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on any, link-type LINUX_SLL2 (Linux cooked v2), snapshot length 262144 bytes
19:50:24.454916 lo    In  IP 127.0.0.1.56080 > 127.0.0.1.4000: Flags [S], seq 2500379491, win 65495, options [mss 65495,sackOK,TS val 1673811859 ecr 0,nop,wscale 7], length 0
19:50:24.454927 lo    In  IP 127.0.0.1.4000 > 127.0.0.1.56080: Flags [S.], seq 1261009642, ack 2500379492, win 65483, options [mss 65495,sackOK,TS val 1673811859 ecr 1673811859,nop,wscale 7], length 0
19:50:24.454937 lo    In  IP 127.0.0.1.56080 > 127.0.0.1.4000: Flags [.], ack 1, win 512, options [nop,nop,TS val 1673811859 ecr 1673811859], length 0
```
