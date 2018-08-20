Process Management
===================

## Introduction

Process management is one of the most important aspects of any Operating Systems.

While most operating systems provide a single system call to create a process, Unix and Linux in general has used a two-step approach, ie. the two system calls `fork()` and `exec()`.

Even though two calls exist, `exec()` is called as soon as `fork()` is executed.

## 1. The Process ID

1. Every process is represented by a Process ID (PID).
2. The PID is guaranteed to be unique at any given point of time.
3. The kernel does not readily allocate a process ID which has been returned back to the pool of free IDs.
4. The `Process scheduler` is a subsystem of the kernel that allocates processes on the CPU.

### 1.1. The idle process
1. The `Process Scheduler` run a special code routine called `idle` as a process on the CPU, if there are no processes to be run. This situation is rare, though.
2. The `idle` process has a PID of 0.

### 1.2. The init process
1. The first process that the kernel start is `init`, with a PID of 1.
2. Unless the default is not explicitly changed with the `init` kernel parameter, the kernel tries to find the `init` binary at the following locations:
    * /sbin/init
    * /etc/init
    * /bin/init
    * /bin/sh

If all four of these are not available, the kernel halts the Linux machine with a panic.

3. A System V machine will have `/sbin/init`, and a Systemd machine will have `/sbin/init` pointing to `/usr/lib/systemd/systemd`.
4. The `init` process is the first process in User-space, and is the parent process of all the processes starting off in user-space.
5. Once `init` starts, the remainder of the boot process proceeds with the starting of system services, login etc.

### 1.3. Process ID allocation
1. The maximum process ID limit imposed by the Linux kernel is *32768*.
2. The limit is for compatibility with older Unix systems, that used 16-bit types for PIDs.
3. The limit can be changed in `/proc/sys/kernel/pid_max`.
4. The PID allocation is in a linear order.
    * If PID 10 is allocated, the next allocated PID is 11, even if PID 10 is not running.
    * PID 10 will be allocated only once the kernel has gone through the PID limit in `/proc/sys/kernel/pid_max`.

### 1.4. The process hierarchy
1. The process that spawns a new process is known as the Parent process.
2. Every process is spawned from another process, except the `init` process.
3. The Parent process ID (PPID) can be listed in `ps`, `top` etc.
4. Each process is owned by a user and group.
5. User and Group mappings are specified in `/etc/passwd` and `/etc/groups`.
6. The kernel is only concerned about the UID/GID and not the user name mappings.

### 1.5. pid_t
1.

### Fetching the Process ID and Parent ID


## Creating/Running a new process



## The Scheduler, IDLE process, and INIT process

* /sbin/init is the first process in
* Just before /sbin/init
* The **idle** process has a PID of 0.
* It is scheduled on a CPU in case there are no processes to be run.
* The first process in user space

