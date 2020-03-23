# Systemd

## Introduction

Systemd is an initialization system for Linux, mostly replacing the traditional `init`.

There have been multiple init systems, but systemd is taking over most of them.

Some of the previous init systems include:

1. Traditional init used by Linux
2. BSD init (Used by FreeBSD, OpenBSD etc.., still used)
3. [Upstart](http://upstart.ubuntu.com/)
4. [Systemd](https://systemd.io/)

## Units and Unit files

### Units

Any entity managed by Systemd is referred to as a `Unit`.

Hence, a Unit can be any of the following:
    * A service
    * A socket
    * A device
    * A mount point
    * A swap file
    * A startup target (runlevel)
    * A swap file or a partition
    * etc..

### Unit files

A unit file controls the behavior of a unit in systemd.

For example, in the example of the unit being a system service, the unit file defines:
    * Location of the executable for the unit
    * How to start the service
    * How to stop the service
    * Defines the dependencies of the service
    * Actions to follow on reload, restart etc..

An example for a unit file: `/usr/lib/systemd/system/sshd.service`

```bash
[vimal@f31 ~]$ cat /usr/lib/systemd/system/sshd.service
[Unit]
Description=OpenSSH server daemon
Documentation=man:sshd(8) man:sshd_config(5)
After=network.target sshd-keygen.target
Wants=sshd-keygen.target

[Service]
Type=notify
EnvironmentFile=-/etc/crypto-policies/back-ends/opensshserver.config
EnvironmentFile=-/etc/sysconfig/sshd-permitrootlogin
EnvironmentFile=-/etc/sysconfig/sshd
ExecStart=/usr/sbin/sshd -D $OPTIONS $CRYPTO_POLICY $PERMITROOTLOGIN
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
RestartSec=42s

[Install]
WantedBy=multi-user.target
```

# References
1. https://wiki.archlinux.org/index.php/Systemd
2. [https://systemd.io/](https://systemd.io/)