# Part 1: GNS3 Configuration with Docker

## Overview

This part creates two Docker images for network simulation in GNS3:
- **Host image**: Lightweight client based on BusyBox
- **Router image**: FRRouting (FRR) with routing daemons enabled

## Build Instructions

```bash
docker build -t router -f ./_mgena_router .
docker build -t host -f ./_mgena_host .
```

After building, import images into GNS3, configure them and run the project. Connect to nodes using the auxiliary console.

## Docker Images

### Host Image (`_mgena_host`)

Minimal BusyBox image providing basic networking tools (`ping`, `ip`, `sh`). Reduces VM load.

### Router Image (`_mgena_router`)

Based on [FRRouting](https://frrouting.org/) v10.5.1 with the following daemons enabled:

**bgpd** — implements BGP (Border Gateway Protocol, RFC 4271). BGP is the primary protocol used for routing between autonomous systems on the Internet. In this project, BGP with MP-BGP extensions carries EVPN routes for MAC address distribution across VXLAN fabric.

**ospfd** — implements OSPF (Open Shortest Path First, RFC 2328). OSPF is a link-state interior gateway protocol that computes shortest paths using Dijkstra's algorithm. Each router maintains a complete topology map of the network. Used in Part 3 for establishing connectivity between routers before BGP peering.

**isisd** — implements IS-IS (Intermediate System to Intermediate System, ISO 10589). IS-IS is another link-state IGP, commonly used by large ISPs. Required by the subject but not actively used in Parts 2-3.

### FRR Architecture

FRRouting uses a modular architecture where each routing protocol runs as a separate daemon process. All protocol daemons communicate with **zebra**, the core routing daemon that manages the Routing Information Base (RIB). Zebra aggregates routes from all protocols, selects the best routes based on administrative distance, and installs them into the Linux kernel's Forwarding Information Base (FIB). The **vtysh** shell provides a unified CLI interface to configure all daemons.

## Key Technologies (Parts 2-3)

### VXLAN (Virtual Extensible LAN)

VXLAN (RFC 7348) is a tunneling protocol that encapsulates Layer 2 Ethernet frames within Layer 3 UDP packets. This allows creating virtual L2 networks over existing L3 infrastructure. Each VXLAN segment is identified by a 24-bit VNI (VXLAN Network Identifier), supporting up to 16 million isolated networks. VTEP (VXLAN Tunnel Endpoint) devices perform encapsulation/decapsulation at network edges.

### EVPN (Ethernet VPN)

EVPN (RFC 7432) is a BGP-based control plane for L2VPN services. Instead of traditional MAC learning via flooding, EVPN distributes MAC/IP information through BGP updates. This eliminates unnecessary broadcast traffic and enables optimal forwarding. Key route types: Type 2 (MAC/IP Advertisement) announces host MAC addresses, Type 3 (Inclusive Multicast) handles BUM (Broadcast, Unknown unicast, Multicast) traffic. Combined with VXLAN, EVPN provides scalable data center fabric with automatic host discovery.
