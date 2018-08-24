<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [File and Directory Management](#file-and-directory-management)
  - [Introduction](#introduction)
  - [1. Files and Metadata](#1-files-and-metadata)
    - [1.1. stat(), lstat(), and fstat()](#11-stat-lstat-and-fstat)
    - [1.2. Permissions [chmod() and fchmod()]](#12-permissions-chmod-and-fchmod)
    - [1.3. Ownership](#13-ownership)
    - [1.4. Extended Attributes (xattrs)](#14-extended-attributes-xattrs)
      - [1.4.1. Keys and Values](#141-keys-and-values)
      - [1.4.2. Extended Attribute Namespace](#142-extended-attribute-namespace)
      - [1.4.3. Extended Attribute Operations](#143-extended-attribute-operations)
  - [2. Directories](#2-directories)
    - [2.1. The current working directory](#21-the-current-working-directory)
    - [2.2. Creating directories](#22-creating-directories)
    - [2.3. Removing directories](#23-removing-directories)
    - [2.4. Reading a directory's content](#24-reading-a-directorys-content)
  - [3. Links](#3-links)
    - [3.1. Hard links](#31-hard-links)
    - [3.2. Soft / Symbolic links](#32-soft--symbolic-links)
    - [3.3. Unlinking](#33-unlinking)
  - [4. Copying/Moving files](#4-copyingmoving-files)
    - [4.1. Copying](#41-copying)
    - [4.2. Moving](#42-moving)
  - [5. Device nodes](#5-device-nodes)
    - [5.1. Special device nodes](#51-special-device-nodes)
  - [6. Virtual File System (Virtual File Switch)](#6-virtual-file-system-virtual-file-switch)
    - [6.1. What is it?](#61-what-is-it)
    - [6.2. Mandatory data structures](#62-mandatory-data-structures)
      - [6.2.1. inode object](#621-inode-object)
      - [6.2.2. superblock object](#622-superblock-object)
      - [6.2.3. dentry object](#623-dentry-object)
      - [6.2.4. file object](#624-file-object)
      - [6.2.5. inode_operations object](#625-inode_operations-object)
      - [6.2.6. superblock_operations object](#626-superblock_operations-object)
      - [6.2.7. dentry_operations object](#627-dentry_operations-object)
      - [6.2.8. file_operations object](#628-file_operations-object)
  - [7. Inodes](#7-inodes)
    - [7.1. Metadata in inodes](#71-metadata-in-inodes)
    - [7.2. Access time, Modify time, Change time](#72-access-time-modify-time-change-time)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

File and Directory Management
==============================

## Introduction


## 1. Files and Metadata


### 1.1. stat(), lstat(), and fstat()


### 1.2. Permissions [chmod() and fchmod()]


### 1.3. Ownership


### 1.4. Extended Attributes (xattrs)


#### 1.4.1. Keys and Values

#### 1.4.2. Extended Attribute Namespace

#### 1.4.3. Extended Attribute Operations

## 2. Directories

### 2.1. The current working directory

### 2.2. Creating directories

### 2.3. Removing directories

### 2.4. Reading a directory's content

## 3. Links

### 3.1. Hard links

### 3.2. Soft / Symbolic links

### 3.3. Unlinking

## 4. Copying/Moving files

### 4.1. Copying

### 4.2. Moving

## 5. Device nodes

### 5.1. Special device nodes

## 6. Virtual File System (Virtual File Switch)

### 6.1. What is it?

### 6.2. Mandatory data structures

#### 6.2.1. inode object

The `inode` object represents an individual file or directory.

#### 6.2.2. superblock object

The `superblock` object represents an entire filesystem. It carries information about the filesystem.

#### 6.2.3. dentry object

The `dentry` object represents an individual directory entry. This is used to point to each entries (file / directory) under a folder.

#### 6.2.4. file object

The `file` object represents an open file.

#### 6.2.5. inode_operations object

#### 6.2.6. superblock_operations object

#### 6.2.7. dentry_operations object

#### 6.2.8. file_operations object

## 7. Inodes

### 7.1. Metadata in inodes

### 7.2. Access time, Modify time, Change time

**Access** Time is when the file was last accessed.

**Modify** Time is when the file was last modified.

**Change** Time is when the metadata of the file was last changed.

Upon creation, all three of these are set to the current time.


## 8. Hard and Soft links


### 8.1. Hard links


### 8.2. Soft links


## 15. I/O Schedulers

The main job of a CPU Scheduler boils down to two specific operations.

* Merging
* Sorting

### 15.1. Merging

Merging is the process of taking two or more I/O adjacent requests and combining them into a single I/O request.

For example, consider three read requests, one from disk sector 20, the second from disk sector 25, and the third from disk sector 30. Even though there are three read requests, the I/O Scheduler can merge these three into one single request for disk sectors 20, 25, 30. This reduces to-and-fro transactions, and since the disk read/write head proceeds sequentially, it is best to merge these requests and get the results in a single go.

The total I/O output might be the same, but the I/O operations are cut down to 1/3 in this case.

### 15.2. Sorting

Sorting is the process of arranging the I/O requests in ascending block order. This is the most important of the two operations.

If read requests come in for blocks 10, 4, 50, 2, 1, and 100, the Sorting algorithm sorts them in ascending order so that a single motion of the read head can read the blocks sequentially.

If the reads were done in the order they were requested, the read/write head would have to move around quite a bit to-and-fro, and multiple I/O operations. This increases the time and wear-and-tear of the head.

Once the I/O requests are sorted, they can be processed sequentially. The seeks are reduced which is usually the bottleneck in disk I/O.


### 15.3. Types of I/O Schedulers

#### 15.3.1. The Linus Elevator (2.4 kernel series)

#### 15.3.2. The Deadline Scheduler

#### 15.3.3. The Anticipatory Scheduler

#### 15.3.4. The CFQ I/O Scheduler (Completely-Fair-Queuing)

#### 15.3.5. The Noop I/O Scheduler
* The most basic of all I/O schedulers.
* Only does basic Merging.
* Does not sort the I/O requests.
* Commonly used for applications reading disks which does not require request sorting, or have their own request sorting implemented internally.





