<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Operating System Concepts](#operating-system-concepts)
- [1. System Programming](#1-system-programming)
  - [1.1. System Calls](#11-system-calls)
    - [1.1.1. Issuing System calls](#111-issuing-system-calls)
  - [1.2. The C library](#12-the-c-library)
  - [1.3. The C compiler](#13-the-c-compiler)
- [2. APIs and ABIs](#2-apis-and-abis)
  - [2.1. APIs](#21-apis)
  - [2.2. ABIs](#22-abis)
- [3. Abstraction](#3-abstraction)
  - [3.1. Files and file systems](#31-files-and-file-systems)
    - [3.1.1. Regular files](#311-regular-files)
    - [3.1.2. Directories](#312-directories)
    - [3.1.3. Hard Links](#313-hard-links)
    - [3.1.4. Soft Links / Symbolic Links](#314-soft-links--symbolic-links)
    - [3.1.5. Special files](#315-special-files)
    - [3.1.6. Namespaces](#316-namespaces)
  - [3.2. Processes](#32-processes)
  - [3.3. Users and Groups](#33-users-and-groups)
  - [3.4. Permissions](#34-permissions)
  - [3.5. Signals](#35-signals)
  - [3.6. Interprocess Communication](#36-interprocess-communication)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

Operating System Concepts
==========================

# 1. System Programming

Notes taken while studying Operating System concepts, with a focus on Unix/Linux systems.

## 1.1. System Calls

System Calls or `syscalls` are function calls made from user-space, in order to avail a feature from the Kernel.

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

### 1.1.1. Issuing System calls

User-space applications are restricted from accessing the kernel space as they wish, in order to impose security. A system call is required for user-space programs to avail features from the kernel.

The kernel presents a series of functions or hooks into its features, so that user-space applications can invoke them as per requirement.

For example, a user-space application wanting to read a file will have to use the `open()` syscall, followed with the `read()` syscall, to read a stream of data from the file.

The kernel provides a mechanism to initiate these syscalls, from user-space. The application can then execute the required function which is usually only allowed in kernel space. The kernel allows nothing more or less than the feature allowed by that specific syscall.

On **i386 architectures**, the following happens:

1. The user-space application loads the syscall ID to the register `%eax`.
2. The required parameters are loaded in `%ebx`, `%ecx`, `%edx`, `%esi`, and `%edi` registers.
3. The user-space application executes the software interrupt **int 0x80**.
4. The interrupt tells the processor to switch to kernel space, for that application.
5. The kernel reads the `%eax` register and executes the respective syscall.
6. The result is returned to the user-space application.

In case the syscall require more than five parameters, a single register such as `ebx` is used to point to a buffer space where all the parameters are loaded.

---

**NOTE:**

* `int` means **interrupt**, and `0x80` the interrupt number.
* The interrupt is transferred to the entity handling that specific interrupt.
* In Linux, the interrupt `0x80` is handled by the kernel and is used to initiate system calls by user-space applications/programs.
* The kernel expects the system-call ID in the `%eax` register.

---



## 1.2. The C library

## 1.3. The C compiler

# 2. APIs and ABIs

## 2.1. APIs

## 2.2. ABIs

Application Binary Interface (API) deals with the binary interfaces on a specific architecture.

ABI defines the following:

  * How an application interact with itself.
  * How an application interact with the kernel.
  * How an application interact with the libraries.

ABI makes sure the below specifications are met properly:

  * Byte ordering
  * Use of Processor registers
  * Linking with libraries
  * Binary-object format.
  * Invocation of system calls.

# 3. Abstraction

Abstraction is a very important concept in Linux and System programming in general.

Any difficult concept can be easily represented with an abstraction layer on top of it.

The most commonly used abstraction models in Unix/Linux are:

* Files
* Process
* Users, Groups, and Permissions

## 3.1. Files and file systems

* The most basic abstraction in a Unix/Linux system.
* Linux adheres to the theme of `everything-is-a-file` philosophy.
* Most interactions are through reading/writing to a file, even though the destination may not always be a proper file.
* Files are opened for reading, writing, or both, and is referenced by a file-descriptor.
* A file-descriptor is a handle pointing to a location in memory (address space).
* The file-descriptors are unique at any time.
* File-descriptors are shared with user-space, and can be accessed by applications/programs.

### 3.1.1. Regular files

* A regular file in Linux, is a stream of bytes (A linear array called Byte-Stream)
* Beyond the stream of byte format, Linux does not specify any standards.
*

### 3.1.2. Directories

### 3.1.3. Hard Links

### 3.1.4. Soft Links / Symbolic Links

### 3.1.5. Special files

### 3.1.6. Namespaces

## 3.2. Processes

## 3.3. Users and Groups

## 3.4. Permissions

## 3.5. Signals

## 3.6. Interprocess Communication