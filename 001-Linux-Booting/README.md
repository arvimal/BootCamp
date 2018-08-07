## How does a Linux machine boots up?

### 1. The Linux Boot process

1. The Boot process starts when a user powers up the machine.

2. The Motherboard sends a signal to the Power Supply

3. Power supply starts up, and regulates itself into the operating voltage.
    * This may take less than a millisecond.

4. The Power supply system sends the [PowerGood](https://en.wikipedia.org/wiki/Power_good_signal) signal to the Motherboard.
    * The ATX specification defines the Power-Good signal as a +5-volt (V) signal generated in the power supply when it has passed its internal self-tests and the output voltages have stabilized.
    * The Power Good signal (power-good) prevents a computer from attempting to operate on improper voltages and damage itself by alerting it to an improper power supply.

5. The Motherboard starts the Processor, once it recieves the `Power Good` signal.

6. The Processor resets its internal registers, and fill it with pre-defined information.
    * 80386 series and later series set the following registers and corresponding data.

```text
        IP (16 Bit register)           - 0xfff0
        CS selector (16 Bit register)  - 0xf000
        CS base (16 Bit register)      - 0xffff0000
```

7. The Processor starts in [Real Mode](https://en.wikipedia.org/wiki/Real_mode).
    * `Real` mode is characterized by a 20-bit segmented memory address space (giving exactly 1 MiB of addressable memory).
    * This gives it unlimited direct software access to all addressable memory, the I/O addresses, and hardware.
    * `Real` mode provides no support for memory protection, multitasking, or code privilege levels. Thus, all x86 CPUs start in `Real mode` with no memory protection, fixed 64 KiB segments, and only 20-bit (1024 KiB = 1 MiB) addressing.

8. The x86 CPU adds both the **CS Selector** and **CS Base** register contents and expects to find the first instruction after reset, there.

    * All the registers, while in 8086, were 16-bit registers.
    * This meant that only 64KiB addresses could be addressed in a single go.
    * The CS (Code Segment) registers had two types (Selector and Base) each 16 bits long.
    * These together (16 bits + 16 bits) were able to address 32 bit address locations.
    * Hence, the 8086 and any x86 CPUs were able to address approximately 4GB of memory.

    * Before starting up, the x86 CPU had cleared its registers and set it to the following values

```text
        IP (16 Bit register)           - 0xfff0
        CS selector (16 Bit register)  - 0xf000
        CS base (16 Bit register)      - 0xffff0000
```

Adding `CS Selector` and `CS Base` values gives `0xfffffff0`, which is `4 GB - 16 Bytes`.

```python3
In [4]: hex(0xffff0000 + 0xfff0)
Out[4]: '0xfffffff0'
```

This address is called the `Reset vector`. The reset vector is the default location a central processing unit will go to find the first instruction it will execute after a reset. The reset vector is a pointer or address, where the CPU should always begin as soon as it is able to execute instructions.

The address contains a `jump` instruction, which points to the BIOS entry point in a `Read-Only Memory` chip (ROM) on the Motherboard. The BIOS is initialized and it starts up.

9. BIOS starts
* Once the BIOS starts, it does the `Power-On Self Test` and verifies all hardware.
* Information on the bootable disk or boot order is maintained in the BIOS.
* If the boot device is a disk, the BIOS tries to find a boot sector. An HDD sector is 512 Bytes.
* On HDDs partitioned with MBR partitioning tables, the first 446 Bytes of the first sector contains the `BootStrap` code.
* On systems using GRUB, the first stage of GRUB is located here, and **is** the `Bootstrap` code.

**NOTE:**
    BIOS/UEFI cannot directly go ahead and read a disk, unless it has some way of addressing them. Almost all HDD manufacturers provide disk hardware that enable BIOS to utilize them, and access the HDD sectors through LBA (Logical Block Addressing). This is comparitively slow, but helps the BIOS to read the disks and pass control over to a Boot Loader.

10. BIOS hands over control to the `Boot Sector code` (aka `Master Boot Code`) (GRUB Stage1)
* BIOS loads the first sector (Sector #0 of 512B) of the bootable disk into RAM.
* It reads the `Bootstrap` code (`boot.img`, in case of GRUB) residing within the first 446 Bytes.
* The control is passed on to the `Bootstrap` code (boot.img) (GRUB Stage1), and executes in memory.

11. GRUB starts
* Once BIOS reads the first sector of the bootable disk via LBA addressing method, the Boot Strap code is called and executed (GRUB Stage1).

* Stage 1 : (`boot.img` in MBR BootStrap code area, ie.. Sector #0)
    * `boot.img` is stored in the master boot record (MBR) or optionally in any of the volume boot records (VBRs), and addresses the next stage by an LBA48 address (thus, the 1024-cylinder limitation of GRUB legacy is avoided).
    * The Stage1 is configured (automatically) to load the first sector of core.img (core.img = Stage 1.5)

* Stage 1.5: (`core.img` - Sector #1 to #62) (Contains filesystem drivers to access partitions with Filesystems, for kernel)
    * `core.img` is by default written to the sectors between the MBR and the first partition, when these sectors are free and available.
    * For legacy reasons, the first partition of a hard drive does not begin at sector #1 (counting begins with 0) but starts at sector #63. This leaves 62 sectors of empty space not part of any partition or file system, and therefore not prone to any problems related with it.
    * Once executed, `core.img` will load its configuration file and any other modules needed, particularly file system drivers; at installation time, it is generated from diskboot.img and configured to load the stage 2 by its file path.

* Stage 2: (In /boot/grub/)
    * GRUB uses the filesystem drivers to access the filesystem partitions, and reads `grub.conf`.
    * Files belonging to Stage 2 are all in `/boot/grub`, which is a subdirectory of the /boot directory
    * This includes the configuration file `grub.cfg`, the kernels (vmlinuz), and the initrd files etc..
    * The GRUB menu as per `grub.conf` is shown and user selects a kernel to boot from.

12. The selected/default kernel loads, and initrd.

* GRUB reads the entry for the kernel selected (by the user or default kernel), and loads the kernel mentioned with directive `linux16` to memory.
* The kernel takes into consideration the kernel parameters set for `vmlinuz`, and acts accordingly.

***
**NOTE:** Some info on `grub.conf` and its parameters.

* The location where GRUB searches for the kernel and initrd, is set with the parameter `set root='hd0, msdos1'`. This is the GRUB root, or the location where GRUB intends to find the kernel and initrd.
* The `search` parameter in grub.conf sets the way the GRUB root should be checked, and the disk UUID it should check. This filesystem is where GRUB expects to find the kernel and initrd.
* The `search` keyword follows with the `vmlinuz` path, and initrd path. A vmlinuz and kernel parameters example:

~~~
`/vmlinuz-3.10.0-693.el7.x86_64 root=/dev/mapper/rhel_dell--r430--19-root ro crashkernel=auto rd.lvm.lv=rhel_dell-r430-19/root rd.lvm.lv=rhel_dell-r430-19/swap rhgb quiet LANG=en_US.UTF-8`
`initrd16 /initramfs-3.10.0-693.el7.x86_64.img`
~~~

* Note that the kernel parameters mention `ro` (read-only). This is normal, and it instructs the kernel to read the root filesystem as read-only so that the filesystem checker can run its checks safely. After the filesystem check, the root filesystem will be mounted as read-write.
* If the kernel comes upon any parameters that it doesn't understand, it saves it and passes it to `init` (or systemd), in order to process later.
* If the vmlinuz path starts with a slash ('/') as in `/vmlinuz-3.10.0-693.el7.x86_64`, it means that the /boot/ partition is different than the root partition ('/'). If /boot/ was on the same partition as root ('/'), the entry would have been `/boot/vmlinuz-3.10.0...`.
***

* The kernel loads the `initrd` file listed in `grub.conf`.
* Initrd contains the necessary drivers for the kernel, to access the connected devices as well as form a virtual filesystem in memory.
* With a virtual filesystem running in memory, the kernel initializes `/sbin/init` (which was part of the initrd file).

13. `init` or `systemd` starts
* `/sbin/init` starts, which is a symlink to `/usr/lib/systemd/systemd` in systems using `Systemd`.
* `init` reads `/etc/inittab` for run levels, and go to the specific runlevel locations at `/etc/init.d/` to start the scripts marked to startup in that level.
* `Systemd` looks for the targets it has to reach, and starts the units for it.
* By default, Systemd is configured to reach the `multi-user` target, and starts the services for it, and presents the login prompt.

***
***

### Notes
_**1. History of Real Mode**_
1. The _80286_ series of processors introduced the `Protected Mode` of operation.
2. `Real Mode` was the operational mode available before `Protected Mode` emerged in 80826.
3. `Protected Mode` enabled features such as virtual memory, paging etc.., and these were not available in `Real Mode` mode of operation.
4. Backward compatibility is a design decision in x86 series, hence it was a requirement to get any software written for processor series before 80286 to be able to run on any x86 series. The existing system software which were written for `Real Mode` would have to be re-written to use the `Protected Mode`.
5. Hence, to maintain backward compatibility with all previous series as well as for using the existing system software, all x86 processors from `80286` till the latest x86 64-bit processors (those using Protected mode), start in `Real Mode`.
6. The Processor switches from `Real Mode` to `Protected Mode` after the system software sets up a few descriptor tables and enables the Protection Enable (PE) bit in the control register 0 (CR0).

_**2. Why was the change to Protected mode required?**_
1. The Intel 8086, the predecessor to the 80286, was originally designed with a 20-bit address bus for its memory.
2. This allowed the processor to access 220 bytes of memory, equivalent to 1 megabyte.
3. At the time, 1 MB of memory was considered a relatively large amount of memory, so the designers of the IBM Personal Computer reserved the first 640 kilobytes for use by applications and the operating system and the remaining 384 kilobytes for the BIOS and memory for add-on devices.
4. As the cost of memory decreased and memory use increased, the 1 MB limitation became a significant problem. Intel intended to solve this limitation along with others with the release of the 286, through the `Protected Mode`.

_**3. How does Real mode address memory?**_
1. `Real Mode` was the only mode available in the series prior 80286, ie.. 8086.
2. This series had 20-bit address buses capable of addressing 1MiB of Memory, but had only 16-bit CPU registers that could load upto 64KiB of memory at a time.
3. This meant that, even though the bus had the width to accomodate a larger bit size and access the memory, the Processor registers were not big enough to load the data in a single go.
4. Hence, 8086 processors used a method known as [Memory Segmentation](https://en.wikipedia.org/wiki/Memory_segmentation).
5. In 8086, Memory segmenatation worked by creating 64KiB chunks of the 1MiB data space, and loading it as required.

_**4. 80826 series provides memory protection compared to 8086, what is it?**_
1. Memory segmentation is the process of dividing the system memory into sections.
2. 8086 series used this technique to address a memory of 1MiB when its registers were only capable of storing 64KiB.
2. In a system that uses memory segmentation, a reference to the memory location would include a value that points to a segment and an offset. This enables the processor to read the entire segment.
3. The `Memory Management Unit` is responsible for mapping the segments to the actual locations in RAM, as well as checking the access permissions for that specific memory location.
4. The Memory Segmentation used by 8086 did not provide any protection. ie. it didn't have a mechanism to prevent access to specific memory segments. Any software running on these processors can access any memory segments as it chose, even if those were not in use by the said system software.
5. The lack of Memory protection in 8086 prevented features such as Virtual Memory from being realized.
6. 80826 came with the `Protected Mode` of operation which brought in memory protection and features such as virtual memory, paging etc.
7. As said earlier, due to backward compatibility, 80286 still started up in `Real mode` even though it came with `Protected Mode`.

_**5. Master Boot Record**_
1. A master boot record (MBR) is a special type of boot sector at the very beginning of a partitioned storage device.
2. The MBR holds the information on how the partitions are organized on that medium.
3. The organization of the partition table in the MBR limits the maximum addressable storage space of a disk to 2 TiB (232 × 512 bytes)
4. The first `446 Bytes` of the sector of an MBR partitioned disk, contains the `Bootstrap` code.
5. The next `four 16 Bytes` contains the four partition entries. Hence the limit of four primary partitions.
6. The final `two bytes` contain the `Boot signature`, which denotes the disk is bootable and acts as an indicator that the sector is ending here.

Thus, **Total size =`446 + (4 x16) + 2` = 512 Bytes**

_**6. Why does GRUB have multiple stages?**_
* GRUB has multiple stages, `Stage 1` being in the `Bootstrap` section of the first sector of MBR formatted bootable disk.
* The Bootstrap code has only a space of around 446 Bytes.
* This is enough for simple bootloaders, but not so for bootloaders that support Menu-drive selection, supports multiple filesystems etc.
* Hence, the first stage of Grub exists in the Bootstrap code area, and the remaining at multiple locations such as the sectors between Sector #0 and Filesystem partition, as well as the Active partition.
* Although every MBR formatted HDD contains an MBR, the master boot code is used only if the disk contains the active, primary partition.

_**7. Difference between GRUB1 (Legacy) and GRUB2**_
1. GRUB1 works only on x86 and x86_64 architecture. GRUB2 works on multiple architectures including SPARC and PowerPC.
2. GRUB1 supported only MBR and BIOS, while GRUB2 supports MBR, GPT, EFI, BIOS, OpenFirmware etc..
3. GRUB1 supported boot from normal filesystems, while GRUB2 supports reading LVM, RAID etc..
4. GRUB1 could only read a few filesystems such as EXT, XFS, JFS, FAT, ReiserFS etc.., while GRUB2 supports additional FS such as Apple FS, NTFS etc.

_**8. Difference between MBR and GPT partition methods**_
    * MBR (also called msdos partitions) uses 32-bits to store Block (LBA) addresses. For HDDs with 512 byte sectors, the MBR partition table entries allow a single partition upto 2TB.
    * GPT (Guid Partition Table) use logical block addressing (LBA) in place of the historical cylinder-head-sector (CHS) addressing.
    * On a system using GPT, the MBR is still maintained for backward compatibility. The protective MBR is contained in LBA 0, the GPT header is in LBA 1, and the GPT header has a pointer to the partition table, or Partition Entry Array, typically LBA 2.
    * The UEFI specification stipulates that a minimum of 16,384 bytes, regardless of sector size, be allocated for the Partition Entry Array.[4] On a disk having 512-byte sectors, a partition entry array size of 16,384 bytes and the minimum size of 128 bytes for each partition entry, LBA 34 is the first usable sector on the disk.

_**9. Difference between BIOS and EFI firmware systems**_

_**10. Troubleshooting GRUB**_
1. Press `e` from GRUB menu, to enter the GRUB configuration and edit it.
2. Check the GRUB root where GRUB checks for the UUID, at the `set root=`hd<X>, <type>` parameter. The `type` would be usually `msdos` for MBR partitions and `gpt` for GPT partitions.
3. Press `Ctrl + C` to access the GRUB command prompt.
    * `ls` and `ls -l` (for more details) to list the partitions on the disks, on the machine.

4. Update/Edit grub.cfg
    * Any changes to grub.cfg won't be permanent. Hence, don't directly edit it.
    *

GRUB 2 works like this: /etc/default/grub contains customization; /etc/grub.d/ scripts contain GRUB menu information and operating system boot scripts. When the update-grub command is run, it reads the contents of the grub file and the grub.d scripts and creates the grub.cfg file.

***
***

### Appendix:
1. https://en.wikipedia.org/wiki/Power_good_signal
2. https://en.wikipedia.org/wiki/Real_mode
3. https://en.wikipedia.org/wiki/Protected_mode
4. https://en.wikipedia.org/wiki/Memory_segmentation
5. https://en.wikipedia.org/wiki/Master_boot_record
6. https://technet.microsoft.com/en-us/library/cc976786.aspx
7.


