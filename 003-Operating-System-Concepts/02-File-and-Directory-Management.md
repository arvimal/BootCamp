<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [File and Directory Management](#file-and-directory-management)
  - [Introduction](#introduction)
  - [1. Files and Metadata](#1-files-and-metadata)
    - [1.1. stat(), lstat(), and fstat()](#11-stat-lstat-and-fstat)
      - [1.1.1. stat()](#111-stat)
      - [1.1.2. fstat()](#112-fstat)
      - [1.1.3. lstat()](#113-lstat)
      - [1.1.4. Timestamp in the stat struct](#114-timestamp-in-the-stat-struct)
    - [1.2. Permissions](#12-permissions)
    - [1.3. Ownership](#13-ownership)
    - [1.4. umask](#14-umask)
    - [1.5. Extended Attributes (xattrs)](#15-extended-attributes-xattrs)
      - [1.5.1. Keys and Values](#151-keys-and-values)
      - [1.5.2. Extended Attribute Namespace](#152-extended-attribute-namespace)
      - [1.5.3. Extended Attribute Operations](#153-extended-attribute-operations)
    - [1.6. Special files](#16-special-files)
      - [1.6.1. Block device files](#161-block-device-files)
      - [1.6.2. Character device files](#162-character-device-files)
      - [1.6.3. Pipes](#163-pipes)
      - [1.6.4. Sockets](#164-sockets)
    - [1.7. Types of file access](#17-types-of-file-access)
      - [1.7.1. Open](#171-open)
      - [1.7.2. Close](#172-close)
      - [1.7.3. Read](#173-read)
      - [1.7.4. Write / Append](#174-write--append)
      - [1.7.5. Seek](#175-seek)
      - [1.7.6. Delete](#176-delete)
  - [2. Directories](#2-directories)
    - [2.1. The current working directory](#21-the-current-working-directory)
    - [2.2. Creating directories](#22-creating-directories)
    - [2.3. Removing directories](#23-removing-directories)
    - [2.4. Reading a directory's content](#24-reading-a-directorys-content)
    - [2.5. Mounting a filesystem](#25-mounting-a-filesystem)
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
  - [8. Hard and Soft links](#8-hard-and-soft-links)
    - [8.1. Hard links](#81-hard-links)
    - [8.2. Soft links](#82-soft-links)
  - [15. I/O Schedulers](#15-io-schedulers)
    - [15.1. Merging](#151-merging)
    - [15.2. Sorting](#152-sorting)
    - [15.3. Problems with simple sorting of I/O requests](#153-problems-with-simple-sorting-of-io-requests)
    - [15.3. Types of I/O Schedulers](#153-types-of-io-schedulers)
      - [15.3.1. The Linus Elevator (2.4 kernel series)](#1531-the-linus-elevator-24-kernel-series)
      - [15.3.2. The Deadline Scheduler](#1532-the-deadline-scheduler)
      - [15.3.3. The Anticipatory Scheduler](#1533-the-anticipatory-scheduler)
      - [15.3.4. The CFQ I/O Scheduler (Completely-Fair-Queuing)](#1534-the-cfq-io-scheduler-completely-fair-queuing)
      - [15.3.5. The Noop I/O Scheduler](#1535-the-noop-io-scheduler)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

File and Directory Management
==============================

## Introduction

Linux tries to adhere to the `Everything-is-a-File` philosophy. A file is the most basic abstraction layer in a Unix/Linux system.

It helps to access streams of data from a destination, either local or remote, and also access to certain devices that does not fall into normal file formats, such as character devices, sockets etc.

A normal file in Linux is just a stream of bytes on the disk. Linux does not impose any sort of restriction or file formats for files.

## 1. Files and Metadata


### 1.1. stat(), lstat(), and fstat()

The syscalls stat(), lstat(), fstat() all return information about a file or folder.

Each system call differs slightly on what sort of handler they act on, but each one of them return file metadata through a **stat** struct.

glibc provides the `stat()` wrapper around the actual `stat()` kernel API end-point.

#### 1.1.1. stat()

`stat()` returns a **stat** struct which contains the following details.

```bash
        struct stat {
            dev_t     st_dev;         /* ID of device containing file */
            ino_t     st_ino;         /* Inode number */
            mode_t    st_mode;        /* File type and mode */
            nlink_t   st_nlink;       /* Number of hard links */
            uid_t     st_uid;         /* User ID of owner */
            gid_t     st_gid;         /* Group ID of owner */
            dev_t     st_rdev;        /* Device ID (if special file) */
            off_t     st_size;        /* Total size, in bytes */
            blksize_t st_blksize;     /* Block size for filesystem I/O */
            blkcnt_t  st_blocks;      /* Number of 512B blocks allocated */

           /* Since Linux 2.6, the kernel supports nanosecond
              precision for the following timestamp fields.
              For the details before Linux 2.6, see NOTES. */

            struct timespec st_atim;  /* Time of last access */
            struct timespec st_mtim;  /* Time of last modification */
            struct timespec st_ctim;  /* Time of last status change */

           #define st_atime st_atim.tv_sec      /* Backward compatibility */
           #define st_mtime st_mtim.tv_sec
           #define st_ctime st_ctim.tv_sec
           };
```

`stat()` can be called on both symbolic links and actual files (hard links as well). ie. it resolves and returns the information of the destination file.

To read more on `stat()`, try:

```bash
# man 2 stat
```

#### 1.1.2. fstat()

While `stat()` takes in a file-name as its argument, `fstat()` takes in a file-descriptor ID as its argument.

It returns the same `stat` struct as the `stat()` syscall.

#### 1.1.3. lstat()

`lstat()` is similar to `stat()` and returns the `stat` struct.

The major difference is when it's used on a symbolic link. `lstat()` returns the information of the symbolic link rather than the file the link is pointing to.

#### 1.1.4. Timestamp in the stat struct

There are three structs within the `stat` struct that shows different time stamps.

* `st_atime` - Last access time (Data or Metadata)
* `st_mtime` - Last modification time (Data and metadata change)
* `st_ctime` - Last change time (Metadata change, not data)

Older linux kernels (prior kernel version 2.5.48) only supported time-stamps with a granularity of upto a **second**.

From kernel version 2.5.48 onwards, all three of the above time stamps support a nano-second precision.

### 1.2. Permissions

Unix/Linux systems handle file/directory access through a series of permission fields.

These permission fields contain three components, and is set for three level of users.

The three permission fields and their corresponding values are:

|Type|Value|
----|----|
|Read (r) | 4 |
|Write (w) | 2 |
|Execute (x0| 1 |


The three level of user access is:
* User
* Group
* Other (Universal / Other accounts on the system)

A normal file listing looks as following:

```bash
ls -l /tmp
total 168
srwxrwxr-x.  1 user1 user1      0 Oct 17 11:46  atom-2dronS-fzeo6.sock
drwx------.  2 user1 user1     40 Oct 17 11:46 'Atom Crashes'
-rw-------.  1 user1 user1    659 Oct 18 23:38  dropbox-antifreeze-hIX0o8
-rw-------.  1 user1 user1    659 Oct 18 23:38  dropbox-antifreeze-Uv7Ebe
-rw-------.  1 user1 user1    575 Oct 17 10:58  dropbox-antifreeze-xr4T0W
-rw-------.  1 user1 user1    579 Oct 18 23:38  dropbox-antifreeze-zDLKfd
```

**NOTE:**

1. A directory can be listed only if the `read` bit is set for it.
2. A directory can be changed **cd** into, only if `execute` bit is set for it.

[chmod() and fchmod()]


### 1.3. Ownership


### 1.4. umask

### 1.5. Extended Attributes (xattrs)


#### 1.5.1. Keys and Values

#### 1.5.2. Extended Attribute Namespace

#### 1.5.3. Extended Attribute Operations

### 1.6. Special files

Apart from the stream of bytes that Linux see as a file, there are a few things that are represented as file objects for the sake of uniformity and adherence to `Everything-is-a-File` philosophy.

These are called special files to identify them from the normal file abstraction, and they are as following:

  1. Block device file
  2. Character device file
  3. Pipes
  4. Sockets

#### 1.6.1. Block device files

A block device file usually points to a device that is accessed as an array of bytes, or in other words, a chunk of data.

Storage devices such as SCSI, SATA, ATA, SAS, or devices such as CD/DVD, Floppy disks etc.. are all accessed in chunks and are addressed as block devices.

#### 1.6.2. Character device files

#### 1.6.3. Pipes

Pipes are of two types:

  * Named Pipes
  * Regular Pipes

Named pipes a

#### 1.6.4. Sockets

The device driver for the block device reads and maps the content of the disk blocks (multiple sectors in a single go), and the application is free to read any block in that range, in any order.

### 1.7. Types of file access

#### 1.7.1. Open

#### 1.7.2. Close

#### 1.7.3. Read

#### 1.7.4. Write / Append

#### 1.7.5. Seek

#### 1.7.6. Delete



## 2. Directories

### 2.1. The current working directory

### 2.2. Creating directories

### 2.3. Removing directories

### 2.4. Reading a directory's content

### 2.5. Mounting a filesystem

A filesystem has to be mounted to the root directory structure in memory, prior accessing a file/folder on that particular filesystem.

The process of mounting is synonymous to creating a link in kernel-space from a directory in the root filesystem to the destination filesystem, by placing a pointer to the root inode of the destination filesystem in the dentry structure of the source filesystem. (Oh GAWD!)

The process of mounting a filesystem can be described as:

  * The Operating system is passed the name of the device and the mount-point.
  * The filesystem structure/signature is read by the kernel to identify the filesystem.
  * The appropriate filesystem driver module is loaded in memory by the kernel.
  * An entry is made in the kernel mount table that links the mount point, the device file, and the filesystem driver.

* To see the list of mounted filesystems, use:

```bash
# cat /proc/mounts
# cat /etc/mtab
```

* To see the filesystems supported by the kernel (as per the modules loaded), use:

```bash
# cat /proc/filesystems
```

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

### 15.3. Problems with simple sorting of I/O requests

We saw that the two fundamental operations of an I/O scheduler are Merging and Sorting. This sorts and merge requests based on ascending block order so as to reduce the read/write head travel through the HDD tracks.

If I/O requests were always sorted as per the ascending block order, imagine a situation were there are a couple of requests for blocks above 100 but new requests come in continuously for blocks below 50. This gives more priority for the lower blocks and the higher block reads may delay from being serviced.

NOTE: Read request latency is always critical, since it has to be fetched from the disk if the page-cache does not carry the data. Write requests are not always problematic, since the application can do the writes and it goes the page-cache without hitting the disk, and thus is much faster.

Thus, schedulers require more complex algorithms to sort read and write I/O rather than simple sorting and merging. Multiple I/O schedulers exist due to this reason, catering to a wide variety of use-cases.

### 15.3. Types of I/O Schedulers

Since the default behaviour of Merging and Sorting does not cut for proper scheduling, many I/O schedulers have been invented and evolved.

#### 15.3.1. The Linus Elevator (2.4 kernel series)

The Linus Scheduler used the same Merge/Sort algorithms, but came upon an improvement by introducing another list which was sorted based on the request time.


#### 15.3.2. The Deadline Scheduler

#### 15.3.3. The Anticipatory Scheduler

#### 15.3.4. The CFQ I/O Scheduler (Completely-Fair-Queuing)

#### 15.3.5. The Noop I/O Scheduler
* The most basic of all I/O schedulers.
* Only does basic Merging.
* Does not sort the I/O requests.
* Commonly used for applications reading disks which does not require request sorting, or have their own request sorting implemented internally.





