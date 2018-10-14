<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Operating System Concepts](#operating-system-concepts)
  - [Introduction](#introduction)
  - [1. System Programming](#1-system-programming)
    - [1.1. System Calls](#11-system-calls)
    - [1.2. The C library](#12-the-c-library)
    - [1.3. The C compiler](#13-the-c-compiler)
  - [2. APIs and ABIs](#2-apis-and-abis)
    - [2.1. APIs](#21-apis)
    - [2.2. ABIs](#22-abis)
  - [3. Concepts of Linux Programming](#3-concepts-of-linux-programming)
    - [3.1. Files and file systems](#31-files-and-file-systems)
    - [3.2. Processes](#32-processes)
    - [3.3. Users and Groups](#33-users-and-groups)
    - [3.4. Permissions](#34-permissions)
    - [3.5. Signals](#35-signals)
    - [3.6. Interprocess Communication](#36-interprocess-communication)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

Operating System Concepts
==========================

# 1. System Programming

## 1.1. System Calls

System Calls or `syscalls` are function calls made from user-space, in order to avail a feature from the Kernel the layers within kernel space.

Examples include a text editor reading/writing to a file on a filesystem within a disk. The disk requires a firware driver, a generic top-level driver such as SCSI, a filesystem driver etc.. which are all dealt within the kernel land.

All the system calls are documented under section 2 of the Linux manual pages. In order to read an introduction, please try:

```bash
# man 2 intro
```

To list all the system calls available on a Linux/Unix machine, execute:

```bash
# man syscalls
# man 2 syscalls
```

System calls may differ between system architecture, but in Linux a large percentage is common between varying architectures.

### 1.1.1. Calling System calls




## 1.2. The C library

## 1.3. The C compiler

# 2. APIs and ABIs

## 2.1. APIs

## 2.2. ABIs

# 3. Concepts of Linux Programming

## 3.1. Files and file systems

## 3.2. Processes

## 3.3. Users and Groups

## 3.4. Permissions

## 3.5. Signals

## 3.6. Interprocess Communication