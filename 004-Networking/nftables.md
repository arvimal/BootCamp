# nftables

* `nftables` replaces `iptables as the next successor.

* It also replaces tools such as:
    * ip6tables
    * arptables
    * ebtables

* Advantages
  * Looking up tables instead of linear table processing as IPtables
  * Single tool for both IPv4 and IPv6 (compared to iptables/ip6tables)
  * New rules applied automatically
  * Possible to debug and trace using `nftrace` ruleset and `nft` tool
  * Compact syntax
  * API for third-party applications (libnftables, libmnl libraries)

## Structure

Similar to the tables `INPUT`, `OUTPUT`, `FORWARD` in `iptables`, `nftables` use `tables` for storing chains. The rules are within the `Chains`.

The nft tool replaces all tools from the previous packet-filtering frameworks. The libnftables library can be used for low-level interaction with nftables Netlink API over the libmnl library.

The iptables, ip6tables, ebtables and arptables tools are replaced by nftables-based drop-in replacements with the same name. While external behavior is identical to their legacy counterparts, internally they use nftables with legacy netfilter kernel modules through a compatibility interface where required.

Effect of the modules on the nftables rule set can be observed using the nft list ruleset command.

Since these tools add tables, chains, and rules to the nftables rule set, be aware that nftables rule-set operations, such as the nft flush ruleset command, might affect rule sets installed using the formerly separate legacy commands.

To quickly identify which variant of the tool is present, version information has been updated to include the back-end name. In RHEL 8, the nftables-based iptables tool prints the following version string:

```bash
$ iptables --version
iptables v1.8.0 (nf_tables)
```

For legacy iptables:

```bash
$ iptables --version
iptables v1.8.0 (legacy)
```

# References
1. https://wiki.archlinux.org/index.php/Nftables
2. https://wiki.nftables.org/wiki-nftables/index.php/Main_Page