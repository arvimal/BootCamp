# NetworkManager



## N.n. NetworkManager plugins

NetworkManager tries to read and write your distribution's normal network configuration files through plugins called "system settings plugins". These plugins allow you to use the file formats you're already comfortable with and the tools you already know how to use to manage your network configuration. You can use NetworkManager's D-Bus interface too.

A generic plugin called 'keyfile' is also provided to read and write configuration that your distro's native file format cannot handle.

These plugins are configured through the /etc/NetworkManager/NetworkManager.conf configuration file. Since the 'keyfile' plugin is built-in, if all you want is the generic NetworkManager configuration file format, you don't need to do anything. But if you'd like to have NetworkManager read and write your distro's normal network configuration files, you might need to enable that plugin if the distribution hasn't done that for you already.

To enable/disable a plugin, add or remove it from the "plugins" line of /etc/NetworkManager/NetworkManager.conf and restart NetworkManager:

```bash
[main]
plugins=ifupdown
```

From the man page of NetworkManager.conf(5)

```text
PLUGINS
Settings plugins for reading and writing connection profiles. The number of available plugins is distribution specific.

    keyfile
        The keyfile plugin is the generic plugin that supports all the connection types and capabilities that NetworkManager has. It writes files out in an .ini-style format in
        /etc/NetworkManager/system-connections. See nm-settings-keyfile(5) for details about the file format.

        The stored connection file may contain passwords, secrets and private keys in plain text, so it will be made readable only to root, and the plugin will ignore files that are readable or writable by any user or group other than root. See "Secret flag types" in nm-settings(5) for how to avoid storing passwords in plain text.

        This plugin is always active, and will automatically be used to store any connections that aren't supported by any other active plugin.

    ifcfg-rh
        This plugin is used on the Fedora and Red Hat Enterprise Linux distributions to read and write configuration from the standard /etc/sysconfig/network-scripts/ifcfg-* files.
        It currently supports reading Ethernet, Wi-Fi, InfiniBand, VLAN, Bond, Bridge, and Team connections. Enabling ifcfg-rh implicitly enables ibft plugin, if it is available.
        This can be disabled by adding no-ibft. See /usr/share/doc/initscripts/sysconfig.txt and nm-settings-ifcfg-rh(5) for more information about the ifcfg file format.

    ifupdown
        This plugin is used on the Debian and Ubuntu distributions, and reads Ethernet and Wi-Fi connections from /etc/network/interfaces.

        This plugin is read-only; any connections (of any type) added from within NetworkManager when you are using this plugin will be saved using the keyfile plugin instead.

    ibft, no-ibft
        These plugins are deprecated and their selection has no effect. This is now handled by nm-initrd-generator.

    ifcfg-suse, ifnet
        These plugins are deprecated and their selection has no effect. The keyfile plugin should be used instead.
```

# References:
1. https://wiki.archlinux.org/index.php/NetworkManager
2. https://wiki.gnome.org/Projects/NetworkManager/SystemSettings
