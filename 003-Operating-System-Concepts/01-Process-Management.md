<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Process Management](#process-management)
  - [Introduction](#introduction)
  - [1. The Process ID](#1-the-process-id)
    - [1.1. The idle process](#11-the-idle-process)
    - [1.2. The init process](#12-the-init-process)
    - [1.3. Process ID allocation](#13-process-id-allocation)
    - [1.4. The process hierarchy](#14-the-process-hierarchy)
    - [1.5. pid_t](#15-pid_t)
    - [1.6. Fetching the Process ID and Parent ID](#16-fetching-the-process-id-and-parent-id)
  - [2. Creating/Running a new process](#2-creatingrunning-a-new-process)
    - [2.1. The fork() system call](#21-the-fork-system-call)
    - [2.2. Copy-On-Write (COW) behaviour of `fork()`](#22-copy-on-write-cow-behaviour-of-fork)
    - [2.3. The exec() system call](#23-the-exec-system-call)
  - [3. Terminating a process](#3-terminating-a-process)
    - [3.1. SIGCHLD signal and the `wait()` system call](#31-sigchld-signal-and-the-wait-system-call)
    - [3.2. Zombie process](#32-zombie-process)
  - [4. Threads and Concurrency](#4-threads-and-concurrency)
    - [4.1. Threads](#41-threads)
      - [4.1.1. Advantages](#411-advantages)
      - [4.1.2. Disadvantages](#412-disadvantages)
      - [4.1.3. Requirements to support threads](#413-requirements-to-support-threads)
    - [4.2. Concurrency](#42-concurrency)
      - [4.2.1. Mutual Exclusion (Mutex)](#421-mutual-exclusion-mutex)
  - [6. Users and Groups](#6-users-and-groups)
    - [6.1. Real, Effective, and Saved UID/GID](#61-real-effective-and-saved-uidgid)
      - [6.1.1. Real ID](#611-real-id)
      - [6.1.2. Effective ID](#612-effective-id)
      - [6.1.3. Saved ID](#613-saved-id)
  - [7. Daemons](#7-daemons)
  - [8. Process Schedulers [CPU Schedulers]](#8-process-schedulers-cpu-schedulers)
  - [Appendix A. References](#appendix-a-references)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

Process Management
===================

## Introduction

Process management is one of the most important aspects of any Operating Systems.

While most operating systems provide a single system call to create a process, Unix and Linux in general has used a two-step approach, ie. the two system calls `fork()` and `exec()`. Even though two calls exist, `exec()` is called as soon as `fork()` is executed.

>**NOTE:**
>Since kernel version 2.3.3, rather than invoking the kernel's fork() system call, the glibc fork() >wrapper calls clone(2) with flags that provide the same effect as the traditional system call.
>A call to fork() is equivalent to a call to clone(2).

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
1. The process ID is represented by the `pid_t` type, defined in the kernel header source file `sys/types.h`.
2. In Linux, `pid_t` is a typedef to the C int type.

### 1.6. Fetching the Process ID and Parent ID
1. The system call `getpid()` returns the Process ID of the process.
2. The system call `getppid()` returns the parent process ID of a process.

## 2. Creating/Running a new process
1. Two system calls, fork() and exec(), are used to create processes in Linux.
2. Creating a new process with fork() is called forking.
3. The second stage is called `Executing` the program, using the `exec()` system call.

### 2.1. The fork() system call
1. The fork() system call is used to create an image (almost exact copy) of an existing process.
2. The new process is called the `Child` of the process it was forked from, the other called `Parent`.
3. The new process, also known as the `Child` process differs from the `Parent` in a few ways.
    * PID - The child process gets a new PID.
    * Stats - The child's resourse statistics are set to 0.
    * File locks - Any file locks used by the parent process are not inherited by the child process.
    * Signals - Any signals in use with the parent are not inherited by the child process.

**NOTE**:
>Linux systems use clone(), a superset of fork that handles threads and includes additional features.
>fork() remains in the kernel for backward compatibility but calls clone() behind the scenes.

### 2.2. Copy-On-Write (COW) behaviour of `fork()`
1. Copy-on-Write is the process in which modern Unix/Linux systems do forking.
2. In early Unix, the kernel copied the following from the Parent process to the Child process.
    * All internal Data Structures of the parent.
    * All the page-table entries of the parent.
    * All the address-space of the parent.
3. The copy process of the initial method was time consuming, from the kernel's pov.
4. Newer kernels use CoW, which creates a reference pointer to the required data, in the Child process address space.
5. As long as there are only read requests to the child address space, the data is served from the Parent address space.
6. This makes it very easy to create/serve multiple child processes very fast.
7. The child process copy is marked read-only.
8. When a new write happens to the address space of the child process, a page-fault happens since being read-only, and the kernel copies over the page to the child address space. This specific page is now marked out of the CoW mode and is no longer shared between the parent and child process.
9. CoW is a lazy algorithm to reduce overhead unless really necessary.
10. Modern hardware architectures support CoW in their Memory-Management-Unit (MMU), hence this process is really fast.

### 2.3. The exec() system call
1. The `exec()` system call is a part of a family of system calls.
2. Only one of the members in the exec family is a system call, the rest are wrappers around it.
3. The `exec()` system call replaces the original process image (text area) with the image of the program mentioned as argument.
4. A successful `exec()` system call changes the following in the child process:
    * The address space.
    * The process image
    * Signal handling in the parent is not inherited.
    * Memory locks in the parent are not inherited.
    * Mapped files are dropped in the child process.

## 3. Terminating a process
1. A process calls the `exit()` system call when it is ready to terminate.
2. When the process exits, the kernel receives the `exit()` routine, and the kernel cleans up all the resources it had used, this includes:
    * Allocated memory
    * Open files
    * System V semaphores
3. After cleanup, the kernel destroys the process and notified the parent of the child's exit with the **SIGCHLD** signal.

### 3.1. SIGCHLD signal and the `wait()` system call
1. The kernel notifies the parent process of the child's exit using the `SIGCHLD` signal.
2. Usually, the parent process does not need to do anything as a response.
3. The parent process, however, can obtain more information from the child process termination using the `wait()` system call.
5. The `wait()` call is used to monitor a state change of a process.
4. Every parent process has to acknowledge the termination of its child thorugh the `wait()` system call.

### 3.2. Zombie process
A process that has been terminated, but not yet waited by its parent process with the `wait()` system call, is a Zombie process.

The kernel maintains a minimal skeleton of the child process even after it's terminated (either properly terminated after execution, or terminated unexpectedly). This include a few basic data structures including the exit state of the child process.

Only after the parent process reads and acknowledges the child process state through the `wait()` system call, is the process really terminated by the kernel. Since some of the basic data structures of the process has to be maintained, a zombie process still consumes some system resources.

In short, a Zombie process is the state of a process that has been terminated but not yet acknowledged by the wait() system call by its parent.

Poorly written software that does not clean up its children through the `wait()` system call tends to create more Zombie processes. In short, Zombie processes lingering in the system has irresponsible parents.

**IMPORTANT:**
The most common explanation of a Zombie process, is that it is a process whose parent has died and does not have an active parent.

But this does not seem to be the case. A process whose parent has died will remain in the process queue as normal.

When a process is terminated, the `init` process will do a recursive search for all the child processes it has spawned, and will parent it. The `init` process will then cleanly acknowledge the process state and terminate the processes if required, or let the process finish its execution.

## 4. Threads and Concurrency

### 4.1. Threads

#### 4.1.1. Advantages

#### 4.1.2. Disadvantages

#### 4.1.3. Requirements to support threads
1. Thread data structure
  * Identify threads
  * Keep track of resource usage
  * Mechanisms to create and manage threads
  * Mechanisms to safely co-ordinate threads so as to not overwrite the same address space.

### 4.2. Concurrency
We know that two processes cannot access each others memory location, due to the way Virtual Memory works. Please refer `03-Memory-Management.md` for more details on this.

Threads share the same memory address space within the process. This makes it really easy since a processor does not need to reload the memory address every time a context switch happens for a thread within the same address space.

But this also brings in some problems. Two threads can write to the same address space, and read it later to find something different from what it expected to see there. This can cause unexpected results. This is a common occurrence in multi-threaded environment.

A method has to be deviced to prevent different threads from accessing the same address location, at the same time. A common technique is to use `Mutual Exclusion`, that allows only a single process to allow writes to an address space.

#### 4.2.1. Mutual Exclusion (Mutex)
Mutual Exclusion is an Operating System concept where the exclusice access to a memory address location is given to only one thread at a time.

## 5. Process priority

### 5.1. Nice and renicing processes

The “niceness” of a process is a numeric hint to the kernel about how the process should be treated in relation to other processes contending for the CPU.

The strange name is derived from the fact that it determines how nice you are going to be to other users of the system.

A high niceness means a low priority for your process: you are going to be nice. A low or negative value means high priority: you are not very nice.

>**NOTE:**
> In Linux, the nice values range from -20 to +19.
> Higher the value, the more nicer you are.
> Lower the value, the less nicer you are.

Example of resetting the nice value:

```bash
# nice -n 19 top
```

## 6. Users and Groups


### 6.1. Real, Effective, and Saved UID/GID

#### 6.1.1. Real ID

#### 6.1.2. Effective ID

#### 6.1.3. Saved ID

## 7. Daemons
Daemons are processes that possess two specific features:

1. The process is a child of init process.
2. The process is not connected to any terminal, thus running in the background waiting for signals/events.

## 8. Process Schedulers [CPU Schedulers]
CPU Schedulers or Process schedulers schedule processes to run on the CPUs, based on various factors such as priority, nice value etc..

From 2.6 kernel versions onwards, Linux uses the `Completely Fair Scheduler`, also known as `CFS`.

### 8.1. Pre-emptive Scheduling and Co-operative Scheduling

#### 8.1.1. Pre-emptive scheduling

#### 8.1.2. Co-operative scheduling

### 8.2. O(1) process/CPU scheduler

### 8.3. Process timeslice

## Appendix A. References
1. Linux System Programming, 2nd Edition - Robert Love
2. Operating System Concepts, 9th Edition - Galvin, Silberschatz
3. Introduction to Operating Systems - Udacity
4. Advance Operating Systems - Udacity
5. [An Introduction to Programming with Threads - Andrew.D.Birrel](http://www.birrell.org/andrew/papers/ThreadsCSharp.pdf)