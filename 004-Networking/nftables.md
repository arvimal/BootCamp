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


# References
1. https://wiki.archlinux.org/index.php/Nftables
2. https://wiki.nftables.org/wiki-nftables/index.php/Main_Page