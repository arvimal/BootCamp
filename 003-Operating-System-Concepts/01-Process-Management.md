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
    - [3.1. SIGCLD signal and the `wait()` system call](#31-sigcld-signal-and-the-wait-system-call)
    - [](#)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

Process Management
===================

## Introduction

Process management is one of the most important aspects of any Operating Systems.

While most operating systems provide a single system call to create a process, Unix and Linux in general has used a two-step approach, ie. the two system calls `fork()` and `exec()`. Even though two calls exist, `exec()` is called as soon as `fork()` is executed.

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
1. A process is terminated by calling the `exit()` system call.
2. When a process exits, the kernel cleans up all the resources it had used, this includes:
    * Allocated memory
    * Open files
    * System V semaphores
3. After cleanup, the kernel destroys the process and notified the parent of the child's exit with the **SIGCHLD** signal.

### 3.1. SIGCLD signal and the `wait()` system call
1. The kernel notifies the parent process of the child's exit using the `SIGCHLD` signal.
2. Usually, the parent process does not need to do anything as a response.
3. The parent process however can obtain more information from the child process termination using the `wait()` system call.
4.

###


