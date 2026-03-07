# netstat

> TL;DR  
`netstat` (network statistics) is a command-line tool used to print network connections, routing tables, interface statistics, masquerade connections, and multicast memberships.

## Core Concept
`netstat` gives DevOps engineers and sysadmins critical visibility into a system's network stack, showing open ports, active connections, and listening services. Although technically deprecated in favor of the `ss` command in modern Linux, `netstat` remains universally recognized and widely used for debugging connectivity issues, verifying service availability, and inspecting network traffic bottlenecks.

## Basic Commands

### List All Sockets (-a)
Displays all active listening and non-listening sockets across all protocols (TCP, UDP, and UNIX domain sockets).

```bash
netstat -a
```

Example Output:
```bash
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN
udp        0      0 localhost:323           0.0.0.0:*
unix  2      [ ACC ]     STREAM     LISTENING     12345    /run/systemd/private
```

### List Only Listening Sockets (-l)
Filters the output to show only sockets that are currently in a "LISTEN" state, waiting for incoming connections from clients.

```bash
netstat -l
```

Example Output:
```bash
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN
tcp6       0      0 [::]:22                 [::]:*                  LISTEN
```

### Disable DNS Resolution (-n)
Forces `netstat` to display numerical IP addresses and port numbers instead of attempting to resolve hostnames, speeding up command execution considerably.

```bash
netstat -n
```

Example Output:
```bash
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 192.168.1.10:22         192.168.1.50:54321      ESTABLISHED
```

### Show Process ID and Program Name (-p)
Reveals the PID and the name of the program/process to which each socket belongs. Note that you generally need root/sudo privileges to see all system processes.

```bash
sudo netstat -p
```

Example Output:
```bash
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address     Foreign Address     State       PID/Program name
tcp        0      0 10.0.0.5:22       10.0.0.12:49152     ESTABLISHED 1125/sshd: root@pts
```

### List TCP Ports Only (-t)
Restricts the output to only show Transmission Control Protocol (TCP) connections, filtering out UDP and UNIX domain sockets.

```bash
netstat -t
```

Example Output:
```bash
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 localhost:postgresql    localhost:43210         ESTABLISHED
```

### List UDP Ports Only (-u)
Restricts the output to only show User Datagram Protocol (UDP) connections, which are connectionless and typically do not have an "ESTABLISHED" state.

```bash
netstat -u
```

Example Output:
```bash
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
udp        0      0 0.0.0.0:bootpc          0.0.0.0:*
udp        0      0 localhost:123           0.0.0.0:*
```

### The Classic Troubleshooting Combo (-tlnp)
Combines TCP (`-t`), Listening (`-l`), Numeric (`-n`), and Program (`-p`). This is the absolute most common DevOps command for quickly identifying what daemon is listening on a specific port.

```bash
sudo netstat -tlnp
```

Example Output:
```bash
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address   Foreign Address   State    PID/Program name
tcp        0      0 0.0.0.0:80      0.0.0.0:*         LISTEN   890/nginx: master p
tcp        0      0 0.0.0.0:22      0.0.0.0:*         LISTEN   1050/sshd
```

### Display Kernel Routing Table (-r)
Shows the system's IP routing table. This functions exactly the same as using the traditional `route` command.

```bash
netstat -r
```

Example Output:
```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
default         _gateway        0.0.0.0         UG        0 0          0 eth0
10.0.0.0        0.0.0.0         255.255.255.0   U         0 0          0 eth0
```

### Display Network Interfaces (-i)
Displays a table of all network interfaces attached to the system and their respective traffic statistics (MTU, RX/TX packets, errors, and drops).

```bash
netstat -i
```

Example Output:
```bash
Kernel Interface table
Iface      MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
eth0      1500  1534123      0      0 0        987654      0      0      0 BMRU
lo       65536    12456      0      0 0         12456      0      0      0 LRU
```

### Show Protocol Statistics (-s)
Prints detailed summary statistics for each major network protocol (IP, ICMP, TCP, UDP). This is incredibly useful for identifying underlying network-level errors, dropped packets, or failed connections over time.

```bash
netstat -s
```

Example Output:
```bash
Ip:
    Forwarding: 2
    12345 total packets received
    0 forwarded
    0 incoming packets discarded
Tcp:
    150 active connection openings
    2300 segments received
    15 bad segments received
```