# BADASS — BGP At Doors of Autonomous Systems is Simple

## Overview

Network simulation project using GNS3 and Docker. Implements VXLAN tunneling with BGP EVPN control plane for scalable data center fabric.

## Project Structure

```
bgp/
├── P1/     GNS3 + Docker setup
├── P2/     VXLAN (static & multicast)
└── P3/     BGP EVPN with Route Reflector
```

## Parts

| Part | Topic | Key Technologies |
|------|-------|------------------|
| **P1** | Environment Setup | Docker, FRRouting, GNS3 |
| **P2** | VXLAN Tunneling | VXLAN, Bridge, Static/Multicast modes |
| **P3** | BGP EVPN | OSPF, BGP, EVPN, Route Reflector |

## Technologies

**VXLAN** (RFC 7348) — encapsulates L2 frames in UDP packets, creating virtual networks over L3 infrastructure.

**BGP EVPN** (RFC 7432) — distributes MAC/IP information via BGP, replacing flood-and-learn with control plane distribution.

**FRRouting** — open-source routing stack providing BGP, OSPF, and IS-IS daemons.

## Quick Start

1. Build Docker images (P1)
2. Import into GNS3
3. Apply configurations from respective part folders
4. Verify connectivity

See individual part READMEs for detailed instructions.
