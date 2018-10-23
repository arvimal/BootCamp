<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Performance Tuning - Notes](#performance-tuning---notes)
- [1. METHODOLOGIES](#1-methodologies)
  - [1.1. Problem Statement Method](#11-problem-statement-method)
  - [1.2. USE method [Utilisation, Saturation, Errors]](#12-use-method-utilisation-saturation-errors)
- [2. TOOLS](#2-tools)
  - [2.1. OBSERVE (Watch system activity)](#21-observe-watch-system-activity)
    - [2.1.1. BASIC OBSERVABILITY TOOLS](#211-basic-observability-tools)
      - [2.1.1.1. _uptime_ (`w` as well) - (A quick overview on load, uptime etc.)](#2111-_uptime_-w-as-well---a-quick-overview-on-load-uptime-etc)
      - [2.1.1.2. _top_ - (Check process status)](#2112-_top_---check-process-status)
      - [2.1.1.3. _ps_ - (Check process status)](#2113-_ps_---check-process-status)
      - [2.1.1.4. _vmstat_ - (Check Memory status)](#2114-_vmstat_---check-memory-status)
      - [2.1.1.4. _iostat_ - (Check block I/O status)](#2114-_iostat_---check-block-io-status)
      - [2.1.1.5. _mpstat_ - (Statistics on multiple processors)](#2115-_mpstat_---statistics-on-multiple-processors)
      - [2.1.1.6. _sar_ (Monitor system usage)](#2116-_sar_-monitor-system-usage)
    - [2.1.2. INTERMEDIATE OBSERVABILITY TOOLS](#212-intermediate-observability-tools)
      - [2.1.2.1. _strace_ (Tracing system calls)](#2121-_strace_-tracing-system-calls)
      - [2.1.2.2. _tcpdump_ (Tracing network packets)](#2122-_tcpdump_-tracing-network-packets)
      - [2.1.2.3. _netstat_ (Services, Ports, Connection states)](#2123-_netstat_-services-ports-connection-states)
      - [2.1.2.4. _pidstat_](#2124-_pidstat_)
      - [2.1.2.5. _lsof_](#2125-_lsof_)
      - [2.1.2.6. _sar_](#2126-_sar_)
    - [2.1.3. ADVANCED OBSERVABILITY TOOLS](#213-advanced-observability-tools)
      - [2.1.3.1. _ss_ - (Socket Statistics)](#2131-_ss_---socket-statistics)
      - [2.1.3.2. _iptraf_](#2132-_iptraf_)
      - [2.1.3.3. _iotop_](#2133-_iotop_)
      - [2.1.3.4. _slabtop_ (Kernel slab-allocator memory usage)](#2134-_slabtop_-kernel-slab-allocator-memory-usage)
      - [2.1.3.5. _perf_ (Multi-tool with CPU and other profiling)](#2135-_perf_-multi-tool-with-cpu-and-other-profiling)
  - [2.2. BENCHMARKING TOOLS (Benchmark the system/component to understand the capability)](#22-benchmarking-tools-benchmark-the-systemcomponent-to-understand-the-capability)
    - [2.2.1. _fio_ - (Filesystem and Disk benchmarking)](#221-_fio_---filesystem-and-disk-benchmarking)
    - [2.2.2. _iperf_ - (Test Network speed and )](#222-_iperf_---test-network-speed-and-)
    - [2.2.3. _CPU Profiling with perf_](#223-_cpu-profiling-with-perf_)
    - [2.2.4. _System Tap_](#224-_system-tap_)
  - [2.3. TUNING TOOLS (Make changes)](#23-tuning-tools-make-changes)
    - [2.3.1. _sysctl_](#231-_sysctl_)
    - [2.3.2. _/sys interface_](#232-_sys-interface_)
    - [2.3.3. Static Performance Tuning](#233-static-performance-tuning)
  - [2.4. CONFIGURATION CHECK](#24-configuration-check)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

Performance Tuning - Notes
===========================

# 1. METHODOLOGIES

The best method is to understand the problem, logically reason where the problem is, and then use the appropriate tools for understanding the problem, and get information.

## 1.1. Problem Statement Method

Try to understand the issue by asking questions.

* What is the exact issue? Explain in your own words?
* Is this applicable for all the users, all the time?
* When did this start happening?
* What changed prior to the problem?
* What was the state before and after the issue?
* What is the environment? Package levels, OS versions etc..

## 1.2. USE method [Utilisation, Saturation, Errors]

Checking the `Utilisation`, `Saturation`, and `Errors` for all the components in the system.

This is a method in which several statistics are captured for all three of these, and compared with a base line of performance. This takes time and won't be super useful in a case where the house is burning down. This can be used to find long standing problems.

# 2. TOOLS

Tools can be divided into four categories.

* Observe
* Benchmark
* Tune
* Configuration check

## 2.1. OBSERVE (Watch system activity)

### 2.1.1. BASIC OBSERVABILITY TOOLS

#### 2.1.1.1. _uptime_ (`w` as well) - (A quick overview on load, uptime etc.)

```bash
[root@centos7-server ~]# uptime
 21:46:46 up  2:20,  3 users,  load average: 0.00, 0.01, 0.05
```

* `uptime`  gives a one line display of the following information.
  * The current time
  * How long the system has been running
  * How many users are currently logged in
  * The system load averages for the past 1, 5, and 15 minutes.

>**A note on LOAD AVERAGE**
>The `Load Average` is the **average number of processes** that are either in a `Runnable` or `Un-interruptible` state.
>This is also called the CPU load average, since it purely depends on the processes executing on the CPU, or being in-queue for the CPU.

Any tool listing the load average reads from `/proc/loadavg`.

The section `/proc/loadavg` in `man proc` states the following:

>The first three fields in this file are load average figures giving the number of jobs in the run queue (state R)
>or waiting for disk I/O (state D) averaged over 1, 5, and 15 minutes.
>They are the same as the load average numbers given by uptime(1) and other programs.

A process is runnable if its either currently being executed on a CPU or is in the queue waiting for a CPU. Un-interruptible processes are the ones which are waiting for a resource to finish its work, such as a disk I/O.

Hence, the system load average is the average number of processes in the RUN queue (either running or in-queue) or waiting for disk I/O, for the past 5/10/15 minutes.

_Since this metric depends on the number of processes in running/un-interruptible/in-queue state, it also means that this metric is based on the CPU, mostly._

The averages are taken over the three time intervals.

**NOTE:**
* This means that a load average of `1` on a single CPU system, means it is loaded all the time.
* On a `4` CPU system, a load average of `1` means, the load_average divided by the number of CPUS `4`. ie.. 0.25% of the time the CPUs are busy, idle 75% of the time.
* In general, if you have a load average number higher than the count of your CPUs, it means that all of them are loaded/saturated and there is still more work.
* In recent times, `Load Average` in Linux is not just the number of processes which are either Running or in Un-interruptible state.
* Linux incorporated the `Uniterruptible Disk I/O` states into the Load Averages, so now the average load is simply not the number of processes on the CPUs.
* Due to this change, it has become more difficult to understand the metric nowadays.

#### 2.1.1.2. _top_ - (Check process status)

```bash
top - 07:08:26 up 23:03,  1 user,  load average: 0.56, 0.71, 0.87
Tasks: 275 total,   1 running, 271 sleeping,   0 stopped,   3 zombie
%Cpu0  :  2.3 us,  0.7 sy,  0.0 ni, 97.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu1  :  1.7 us,  1.7 sy,  0.0 ni, 90.7 id,  5.7 wa,  0.3 hi,  0.0 si,  0.0 st
%Cpu2  :  1.7 us,  0.3 sy,  0.0 ni, 97.3 id,  0.7 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu3  :  1.0 us,  0.3 sy,  0.0 ni, 98.3 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
%Cpu4  :  1.3 us,  0.3 sy,  0.0 ni, 98.0 id,  0.0 wa,  0.3 hi,  0.0 si,  0.0 st
%Cpu5  : 39.4 us,  3.0 sy,  0.0 ni, 57.0 id,  0.3 wa,  0.3 hi,  0.0 si,  0.0 st
%Cpu6  :  1.7 us,  0.7 sy,  0.0 ni, 97.3 id,  0.0 wa,  0.3 hi,  0.0 si,  0.0 st
%Cpu7  :  1.0 us,  0.3 sy,  0.0 ni, 98.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem : 16114840 total,   793288 free,  4860352 used, 10461200 buff/cache
KiB Swap:  7991292 total,  7991292 free,        0 used.  9679644 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
 2076 user1     20   0 5844332 743308  45804 S  44.9  4.6 689:16.36 dropbox
 1221 root      20   0  656032 208164 157164 S   3.0  1.3  83:51.68 Xorg
 3154 user1     20   0 2203336 154000  93904 S   2.0  1.0   4:57.33 vlc
29632 user1     20   0 1000148 116444  47256 S   1.7  0.7   2:19.23 sublime_text
 1834 user1     20   0  366944  36916  22960 S   1.0  0.2   1:50.51 xfwm4
 1839 user1     20   0  622212  43896  25692 S   1.0  0.3   0:34.04 xfce4-panel
 2840 user1     20   0 1402184 316604 205332 S   1.0  2.0  52:50.95 Wavebox
29961 user1     20   0 2979632 575892 132404 S   1.0  3.6  33:14.81 firefox
  904 root     -51   0       0      0      0 D   0.7  0.0   0:45.61 irq/33-rmi4_smb
11177 user1     20   0   45332   4132   3364 R   0.7  0.0   0:02.07 top
29493 user1     20   0 3447064 323008 281608 S   0.7  2.0   7:25.93 VirtualBox
```

* `top` reads from `/proc/` to fetch the details every `n` second.

* `top` presents information in four sections

1. UPTIME and LOAD AVERAGE
2. TASK and CPU STATES
3. MEMORY USAGE
4. PROCESS STATE

* Section 1 - UPTIME and LOAD AVERAGE

```bash
top - 07:58:29 up 23:53,  1 user,  load average: 0.78, 0.78, 0.86
```
This section contains a single line stating

1. Command name : `top`
2. System time
3. Uptime (in hours:minutes)
4. Number of users
5. Load average (last 1 min, 5 min, 15 minutes) _NOTE: Read more on load average in Section 2.1.1._

* Section 2 - TASK and CPU STATES

This section consists of two lines. One for the tasks, and other for CPU state.

```bash
Tasks: 279 total, 1 running, 275 sleeping, 0 stopped, 3 zombie
%Cpu(s):  9.9 us,  1.0 sy,  0.0 ni, 88.3 id,  0.6 wa,  0.1 hi,  0.0 si,  0.0 st
```

The `Tasks` section lists the number of processes, the running, sleeping, stopped, and Zombie processes.

The `CPU` section shows percentage information on the following:

* `us` - Time running on un-niced User processes.
* `sy` - Time running on system (kernel) processes.
* `ni` - Time running `niced` User processes.
* `id` - Time spent in Idle mode
* `wa` - Time spent waiting for I/O (iowait)
* `hi` - Time spent servicing Hardware Interrupts
* `si` - Time spent servicing Software Interrupts
* `st` - Time stolen by the Hypervisor from the current VM (only applicable if the host is a VM)

* Section 3 - MEMORY USAGE

* This shows the memory usage, ideally in KiBytes, but can be changed with switch `E`.

_1 KiB = kibibyte = 1024 bytes_

```bash
KiB Mem : 16114840 total,  1709324 free,  4353892 used, 10051624 buff/cache
KiB Swap:  7991292 total,  7991292 free,        0 used. 10262616 avail Mem
```

* The first field shows information on **Physical Memory**, `total`, `free`, `used`, and `buffers/cache`.

* The second field shows information on **Virtual Memory**, `total`, `free`, `used`, and `avail` (which is physical memory)

**NOTE:**
1. The `avail` number the second line is an estimation of physical memory available for starting new applications, without swapping.
2. Unlike the `free` field, it attempts to account for readily reclaimable page cache and memory slabs.

* Section 4 - PROCESS INFORMATION

By default, the processes displayed are ordered according to the percentage of CPU usage.

```bash
PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
  1 root      20   0  193968   7132   4068 R   1.0  0.2   0:17.59 systemd
792 dbus      20   0   98380   2092   1436 R   0.7  0.1   1:46.02 dbus-daemon
482 root      20   0   37972   6492   6140 S   0.3  0.2   0:05.31 systemd-journal
```

The fields displayed:

1. PID
The task's unique process ID, which periodically wraps, though never restarting at zero.  In kernel terms, it  is  a  dispatchable entity defined by a task_struct.

2. USER
The effective user name of the task's owner.

3. PR (PRIORITY)
The scheduling priority of the task.  If you see `rt` in this field, it means the task is running under real time scheduling  priority.

4. NI (NICE value)
The  nice  value  of  the  task.  A negative nice value means higher priority, whereas a positive nice value means lower priority.
Zero in this field simply means priority will not be adjusted in determining a task's dispatch-ability.

5. VIRT (Virtual memory usage)
The total amount of virtual memory used by the task.
It includes all code, data and shared libraries plus pages  that  have  been swapped out and pages that have been mapped but not used.

6. RES (Resident Memory Size (KiB))
The non-swapped physical memory a task is using.

7. SHR (Shared memory)
The amount of shared memory available to a task, not all of which is typically resident.
It simply reflects memory that could  be potentially shared with other processes.

8. S (Process State)
The states the process is in. Valid fields can be:

D = Uninterruptible sleep
R = Running
S = Sleeping
T = Stopped by job control signal
t = Stopped by debugger during trace
Z = Zombie

**NOTE:**
Tasks shown as running should be more properly thought of as ready to run  --  their task_struct  is  simply  represented  on  the Linux  run-queue.

9. %CPU (Percentage of CPU - accumulated in case of multiple CPUs)
The task's share of the elapsed CPU time since the last screen update, expressed as a percentage of total CPU time.
In a true SMP environment, if a process is multi-threaded and top is not operating in Threads mode, amounts greater than 100%  may be reported.  You toggle Threads mode with the `H` interactive command.

10. %MEM (Resident Memory)
A task's currently used share of available physical memory.

11. TIME
Total CPU time the task has used since it started. When Cumulative mode is On, each process is listed with the cpu time  that  it and its dead children have used.

12. COMMAND
Display the command line used to start a task or the name of the associated program.


#### 2.1.1.3. _ps_ - (Check process status)


#### 2.1.1.4. _vmstat_ - (Check Memory status)

`vmstat` lists information about `processes`, `virtual memory` (memory, swap, buffers, cache), swap, interrupts, context switching, and CPU information.

```bash
[root@centos7-server ~]# vmstat 2 5
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 212184   2108 667392    0    0     5    11   23   24  0  0 100  0  0
 0  0      0 212168   2108 667392    0    0     0     0   56   58  0  0 100  0  0
 0  0      0 212168   2108 667392    0    0     0     0   54   55  0  0 100  0  0
 0  0      0 212168   2108 667392    0    0     0     0   56   57  0  0 100  0  0
 0  0      0 212168   2108 667392    0    0     0     0   58   57  0  0 100  0  0
```

Description for each fields

1. Procs (Processes)
* r: The number of runnable processes (running or waiting for run time).
* b: The number of processes in uninterruptible sleep.

2. Memory
* swpd: the amount of virtual memory used.
* free: the amount of idle memory.
* buff: the amount of memory used as buffers.
* cache: the amount of memory used as cache.

3. Swap
* si: Amount of memory swapped in from disk (/s).
* so: Amount of memory swapped to disk (/s).

4. IO
* bi: Blocks received from a block device (blocks/s).
* bo: Blocks sent to a block device (blocks/s).

5. System
* in: The number of interrupts per second, including the clock.
* cs: The number of context switches per second.

6. CPU (In percentages of total CPU time)
* us: Time spent running non-kernel code.  (user time, including nice time)
* sy: Time spent running kernel code.  (system time)
* id: Time spent idle.  Prior to Linux 2.5.41, this includes IO-wait time.
* wa: Time spent waiting for IO.  Prior to Linux 2.5.41, included in idle.
* st: Time stolen from a virtual machine.  Prior to Linux 2.6.11, unknown.


#### 2.1.1.4. _iostat_ - (Check block I/O status)


#### 2.1.1.5. _mpstat_ - (Statistics on multiple processors)

```bash
# mpstat -P ALL
Linux 4.13.5-200.fc26.x86_64 (montypython)  10/24/2017  _x86_64_    (8 CPU)

11:08:17 AM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
11:08:17 AM  all    9.52    0.38    1.07    1.27    0.12    0.05    0.00    0.00    0.00   87.60
11:08:17 AM    0   10.33    0.58    1.23    0.19    0.13    0.08    0.00    0.00    0.00   87.47
11:08:17 AM    1    5.81    0.21    0.70    6.61    0.14    0.11    0.00    0.00    0.00   86.42
11:08:17 AM    2   16.60    0.40    1.48    0.58    0.14    0.03    0.00    0.00    0.00   80.77
11:08:17 AM    3    7.67    0.12    1.06    2.04    0.12    0.02    0.00    0.00    0.00   88.97
11:08:17 AM    4   15.50    0.56    1.59    0.33    0.14    0.03    0.00    0.00    0.00   81.86
11:08:17 AM    5    5.11    0.32    0.60    0.08    0.09    0.06    0.00    0.00    0.00 O  93.76
11:08:17 AM    6   11.06    0.63    1.26    0.28    0.13    0.02    0.00    0.00    0.00   86.62
11:08:17 AM    7    4.07    0.21    0.63    0.06    0.07    0.02    0.00    0.00    0.00   94.95
```

#### 2.1.1.6. _sar_ (Monitor system usage)

### 2.1.2. INTERMEDIATE OBSERVABILITY TOOLS

#### 2.1.2.1. _strace_ (Tracing system calls)

#### 2.1.2.2. _tcpdump_ (Tracing network packets)

#### 2.1.2.3. _netstat_ (Services, Ports, Connection states)

#### 2.1.2.4. _pidstat_

#### 2.1.2.5. _lsof_

#### 2.1.2.6. _sar_

### 2.1.3. ADVANCED OBSERVABILITY TOOLS

#### 2.1.3.1. _ss_ - (Socket Statistics)

#### 2.1.3.2. _iptraf_

#### 2.1.3.3. _iotop_

#### 2.1.3.4. _slabtop_ (Kernel slab-allocator memory usage)

#### 2.1.3.5. _perf_ (Multi-tool with CPU and other profiling)

## 2.2. BENCHMARKING TOOLS (Benchmark the system/component to understand the capability)

Benchmarking is difficult to get right, since it's difficult to measure a specific component without the interference of other components.

A good methodology would be to run the benchmark for a long time (hours perhaps) and then use the `Observability` tools parallely to understand how the subsystem and system behaves.

### 2.2.1. _fio_ - (Filesystem and Disk benchmarking)

### 2.2.2. _iperf_ - (Test Network speed and )

### 2.2.3. _CPU Profiling with perf_

```bash
# perf record       # Switches depending on the case
```

### 2.2.4. _System Tap_

## 2.3. TUNING TOOLS (Make changes)

### 2.3.1. _sysctl_

### 2.3.2. _/sys interface_

### 2.3.3. Static Performance Tuning

## 2.4. CONFIGURATION CHECK


