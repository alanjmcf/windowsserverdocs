---
title: Pktmon support for Wireshark (pcapng)
description: Use this topic to analyze Packet Monitor-generated pcapng logs with Wireshark.
ms.topic: how-to
author: khdownie
ms.author: v-kedow
ms.date: 10/27/2021
---

# Pktmon support for Wireshark (pcapng)

>Applies to: Windows Server 2022, Windows Server 2019, Windows 10, Azure Stack Hub, Azure, Azure Stack HCI, versions 21H2 and 20H2

Packet Monitor (Pktmon) can convert logs to pcapng format. These logs can be analyzed using Wireshark (or any pcapng analyzer); however, some of the critical information could be missing in the pcapng files. This topic explains the expected output, and how to take advantage of it.

## Pktmon etl2pcap syntax

Use the following commands to convert the pktmon capture to pcapng format.

```powershell
C:\Test> pktmon etl2pcap help
pktmon etl2pcap <file> [--out <name>] [--drop-only] [--component-id <id>]
    Convert log file to pcapng format.
    Dropped packets are not included by default.

-o, --out
    Name of the formatted pcapng file.

-d, --drop-only
    Convert dropped packets only.

-c, --component-id
    Filter packets by a specific component ID.

Example: pktmon etl2pcap C:\tmp\PktMon.etl -d -c nics
```

## Output filtering

All the information about the packet drop reports, and packet flow through the networking stack will be lost in the pcapng output. Therefore, log contents should be carefully pre-filtered for such conversion. For example:

- Pcapng format doesn't distinguish between a flowing packet and a dropped packet. To separate all the packets in the capture from dropped packets, generate two pcapng files; one that contains all the packets ("**pktmon etl2pcap log.etl --out log-capture.etl**"), and another that contains only dropped packets ("**pktmon etl2pcap log.etl  --drop-only --out log-drop.etl**"). This way you will be able to analyze the dropped packets in a separate log.
- Pcapng format doesn't distinguish between different networking components where a packet was captured. For such multilayered scenarios, specify the desired component ID in the pcapng output "**pktmon etl2pcap log.etl --component-id 5**". Repeat this command for each set of component IDs that you are interested in.
