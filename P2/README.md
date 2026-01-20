Helpers:
To actually see the broadcast ARP call we may need to flush arp cache.

To do so on routers:

`bridge fdb flush dev br0`

And on hosts:
`ip neigh flush dev eth0`

To debug VxLAN:

`ip -d link show vxlan10`

In this part of task we set up a VXLAN tunnel between two routers. And after it 
we add a bridge between routers' interfaces so that we can use them as a single 
network. Our final phisical topology looks like this:
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