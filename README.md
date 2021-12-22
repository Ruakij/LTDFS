ltdfs - Latency-tolerant distributed-filesystem
=================

<br>

Table of Contents
=================
<!-- TOC -->
- [1. Description](#1-description)
    - [1.1. The Idea](#11-the-idea)
<!-- /TOC -->

<br>

# 1. Description

This project is a network-filesystem based on FUSE which can be spanned across multiple locations, also with "higher" latency (up to 50-100ms). (although performance is expected to drop in this sync-mode)

This is for me a deeper dive into the C language and FUSE with the library [libfuse](https://github.com/libfuse/libfuse).

<br>

## 1.1. The Idea

1\. The idea is to connect multiple sites in a mesh-network-topology.

2\. The filesystem makes all files from all sites available at each site in a combined view.

```
/mnt/ltdfs/
\ siteA-Readme.md
\ siteB-Readme.md
```

3\. Each file has certain metadata to identify location and owner.

```
siteA-Readme.md
---
Availability:
  - SiteA
  - SiteB
Owner: SiteA
```

4\. When reading, first the local cache is checked and then one or even multiple sites are chosen to load the file. 

5\. When writing, if the file is available locally, an ownership-transfer is requested with the owner-site.<br>
When the Ack comes back, the file is available to be written to **locally**.<br>
Ownership-transfers are synced immediately through the network from the owner-site.

If the file was not available locally, a Write-lock-request is send to the owner-site.<br>
When the Ack comes back, the file will be written **write-through**.<br>
Lock-Requests are not synced and expire after no data has flown for a period of time.<br>
Writing remotely to a file without a lock-request is rejected.
