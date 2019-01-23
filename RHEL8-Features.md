## New features in RHEL8

### 1. Channels
    BaseOS - Provides core set of underlying OS functionality, the foundation for all installations.
    AppStream - Content in Application Stream includes additional user space applications, runtime languages, and databases in support of the varied workloads and use cases
        * Two formats : RPMs and Modules

### 2. Anaconda
    LUKS2

### 3. Composer
    Build images for Qemu, AWS, Azure, ISO, EXT4 fs etc.
    Half way through building docker images

### 4. Virtualization
    VMs created/managed through Cockpit

### 5. Networking
    nftables replacing iptables

### 6. Kernel
    Early Kdump
        * Includes the vmlinuz and initramfs of kdump in the main initramfs
        * Helps to capture early kernel crashes (Previously the load time was too late)

### 7. Package management
    * Yum based on DNF
        * Improved performance
        * Stable API