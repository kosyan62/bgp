# Part 3: Discovering BGP with EVPN

## Overview

This part implements BGP EVPN (RFC 7432) as a control plane for VXLAN. Instead of static configuration or multicast, BGP automatically distributes MAC address information across the fabric using a Route Reflector.

## Topology

```
                     mgena-1
                 Route Reflector
                    1.1.1.1
                   /   |   \
                  /    |    \
            eth0/   eth1|   eth2\
               /       |        \
          mgena-2   mgena-3   mgena-4
           VTEP      VTEP      VTEP
          1.1.1.2   1.1.1.3   1.1.1.4
            |         |         |
          Host1     Host2     Host3
```

## Key Terms

**AS (Autonomous System)** — a group of networks under single administrative control, identified by a unique number. All routers in this project share AS 65000 (private range).

**Route Reflector (RR)** — a BGP router that redistributes routes between iBGP peers, eliminating the need for full-mesh connectivity.

**VTEP (VXLAN Tunnel Endpoint)** — a device that encapsulates/decapsulates VXLAN traffic. Leaf routers in this topology.

**Loopback** — a virtual interface used as a stable router identifier. BGP sessions are established between loopback addresses for resilience.

**EVPN Route Types**:
- Type 2: MAC/IP Advertisement — announces host MAC addresses
- Type 3: Inclusive Multicast — announces VTEP membership for a VNI

## Setup

Uses Docker images built in Part 1. VTEP configuration has two parts: Linux commands (bridge, VXLAN) and FRR commands (OSPF, BGP).

```bash
# On VTEP — run Linux commands first, then enter vtysh for FRR config
sh _mgena-2

# On host
sh _mgena-1_host
```

## Configuration Components

### Route Reflector (mgena-1)

Runs OSPF and BGP only (no VXLAN):
- OSPF: distributes loopback reachability
- BGP: peers with all VTEPs, reflects EVPN routes between them

### VTEP (mgena-2/3/4)

Runs OSPF, BGP, and VXLAN:
- Linux: bridge + VXLAN with `nolearning` (MAC learning delegated to EVPN)
- OSPF: establishes IP connectivity to RR
- BGP: single session to RR, advertises local VNIs via `advertise-all-vni`

## Verification

```bash
# Show BGP EVPN routes
vtysh -c "show bgp l2vpn evpn"

# Show BGP neighbors
vtysh -c "show bgp summary"

# Show EVPN VNI information
vtysh -c "show evpn vni"

# Show learned MAC addresses
bridge fdb show dev vxlan10
```
