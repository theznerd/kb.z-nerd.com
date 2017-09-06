---
Title: "PXE"
date: 2017-09-05T15:26:39-06:00
draft: false
weight: 1
---
While there are a couple methods to boot a bare metal machine (or a reimage) I like the convenience of PXE. However, it doesn't come without it's pitfalls. The process for PXE booting works similar to this:
{{% panel footer="Jason Sandys: [Link](https://social.technet.microsoft.com/Forums/systemcenter/en-US/78781ba5-b7c2-4201-9880-5368be145882/sccm-pxe-and-dhcp?forum=configmgrgeneral)" %}}
1. Client powers on
2. NIC initiates PXE process
3. NIC acquires DHCP address
4. NIC contacts PXE server (WDS)
5. PXE server delivers NBP to client
6. NIC TFTPs boot image (from WDS)
{{% /panel %}}

At step 3, if DHCP Options 66 and 67 are configured, the NIC will boot to the information returned by DHCP. If those options are not configured the NIC will send a broadcast message out asking for a PXE service point. This becomes a problem if the PXE server (SCCM) is not on the same subnet as the device requesting the PXE boot. This is where IP helpers come into play.

### IP Helpers
If your DP/PXE server does not reside on the same subnet as the device that is PXE booting you will have to setup an IP Helper (or configure DHCP Options 66/67 - but this is not recommended by Microsoft). An IP helper (setup on a Layer 3 device - typically the router for the subnet) will basically take this broadcast packet and make sure that the request gets to the appropriate device on the appropriate subnet.
