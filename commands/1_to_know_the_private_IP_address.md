To find the **private IP address** of a machine (assuming you're on a Linux-based system), you can use the following command:

```bash
hostname -I
```

This will return the private IP address (or addresses) of the system. 

Alternatively, you can use:

```bash
ip a
```

Look for the `inet` entry under the network interface (usually `eth0` or `ens3`), which will show the private IP address. For example:

```bash
inet 192.168.1.10/24
```

The `192.168.1.10` would be the private IP address.

If you're using **Windows**, open Command Prompt and run:

```bash
ipconfig
```

Look for the **IPv4 Address** under the appropriate network adapter.
