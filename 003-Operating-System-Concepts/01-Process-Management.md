Process Management
===================

## Introduction

Process management is one of the most important aspects of any Operating Systems.

While most operating systems provide a single system call to create a process, Unix and Linux in general has used a two-step approach, ie. the two system calls `fork()` and `exec()`.

Even though two calls exist, `exec()` is called as soon as `fork()` is executed.

## The Process ID

1. Every process is represented by a Process ID (PID).
2. The PID is guaranteed to be unique at any given point of time.
3. The kernel does not readily allocate a process ID which has been returned back to the pool of free IDs.
4. The `Process scheduler` is a subsystem of the kernel that allocates processes on the CPU.

### The idle process
1. The `Process Scheduler` run a special code routine called `idle` as a process on the CPU, if there are no processes to be run. This situation is rare, though.
2. The `idle` process has a PID of 0.

### The init process
1. The first process that the kernel start is `init`, with a PID of 1.
2. Unless the default is not explicitly changed with the `init` kernel parameter, the kernel tries to find the `init` binary at the following locations:
    * /sbin/init
    * /etc/init
    * /bin/init
    * /bin/sh

If all four of these are not available, the kernel halts the Linux machine with a panic.

3. A System V machine will have `/sbin/init`, and a Systemd machine will have `/sbin/init` pointing to `/usr/lib/systemd/systemd`.



### Process ID allocation


### The process hierarchy

### pid_t

### Fetching the Process ID and Parent ID


## Creating/Running a new process



## The Scheduler, IDLE process, and INIT process

* /sbin/init is the first process in
* Just before /sbin/init
* The **idle** process has a PID of 0.
* It is scheduled on a CPU in case there are no processes to be run.
* The first process in user space

