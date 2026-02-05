# Part 2: Discovering VXLAN

## Overview

This part sets up a VXLAN (RFC 7348) tunnel between two routers, creating a virtual L2 network over L3 infrastructure. Two modes are implemented: **static** (point-to-point) and **multicast** (dynamic discovery).

## Topology

```
OVERLAY (L2/L3 for hosts):
  30.1.1.1/24  <--- appears like one LAN --->  30.1.1.2/24

          |                                         |
        eth0                                       eth0
          |                                         |
        eth1                                       eth1
         br0                                       br0
          \                                       /
           \                                     /
            vxlan10 (VNI 10) ===== VXLAN ===== vxlan10 (VNI 10)

            UNDERLAY (IP transport between VTEPs):
           10.0.0.1/24 ---- Switch ---- 10.0.0.2/24
               eth0                     eth0
```

**Underlay**: Physical IP network (10.0.0.0/24) connecting routers.

**Overlay**: Virtual L2 network (30.1.1.0/24) where hosts communicate as if on the same switch.

## Setup

Uses Docker images built in Part 1. Apply configuration by running commands from the config files on each node:

```bash
# On router (static mode example)
sh _mgena-1_s

# On host
sh _mgena-1_host
```

## VXLAN Modes

### Static Mode (`_s` files)

```bash
ip link add vxlan10 type vxlan id 10 dev eth0 remote 10.0.0.2 dstport 4242
```

The `remote` parameter specifies the peer VTEP IP address explicitly. Simple point-to-point tunnel. Does not scale well — each peer must be configured manually.

### Multicast Mode (`_g` files)

```bash
ip link add vxlan10 type vxlan id 10 dev eth0 group 239.1.1.1 dstport 4242
```

The `group` parameter specifies a multicast group instead of a single peer. All VTEPs join the group and discover each other automatically. BUM (Broadcast, Unknown unicast, Multicast) traffic is sent to the group; known unicast goes directly to the destination VTEP.

## Verification

```bash
# Ping between hosts
ping 30.1.1.2

# Show VXLAN interface details
ip -d link show vxlan10

# Show bridge MAC address table
bridge fdb show dev vxlan10
```

To observe ARP broadcast traffic, flush the caches first:

```bash
# On routers
bridge fdb flush dev br0

# On hosts
ip neigh flush dev eth0
```
