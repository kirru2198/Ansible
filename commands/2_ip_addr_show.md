The command `ip addr show` (or `ip a show`) is used to display detailed information about the network interfaces and their associated IP addresses on a Linux system. It shows the IP addresses (both IPv4 and IPv6) assigned to each network interface, along with other relevant network configuration details.

Here's a breakdown of what it shows:

- **Interface names**: For example, `eth0`, `ens33`, `wlan0`, etc.
- **IP addresses**: Both IPv4 and IPv6 addresses assigned to the interfaces.
- **Network mask**: Indicating the subnet mask (e.g., `/24`).
- **MAC address**: The hardware address of the interface.
- **Status**: Whether the interface is up (active) or down (inactive).
  
Example output might look like:

```bash
$ ip addr show
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    inet 192.168.1.10/24 brd 192.168.1.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::250:56ff:fe8a:42c4/64 scope link
       valid_lft forever preferred_lft forever
```

In this example:
- `192.168.1.10/24` is the **IPv4 address** of the `eth0` interface.
- `fe80::250:56ff:fe8a:42c4/64` is the **IPv6 address**.

This command provides all the network interfaces and their current configuration on the system, including private and public IPs.
