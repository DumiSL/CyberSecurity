# RDP Enumeration — Windows 7 Target

> **Category:** Network Enumeration / Service Discovery  
> **Target OS:** Windows 7 Home Basic  
> **Tools Used:** Nmap  

---

## Overview

This writeup documents the process of enumerating the Remote Desktop Protocol (RDP) service on a Windows 7 virtual machine, including the troubleshooting steps taken to identify why RDP was not appearing in scan results.

---

## Step 1 — Nmap Scan

Ran an Nmap scan against the target to enumerate open ports and running services.

```bash
nmap -sV -p- <target-ip>
```

**Observation:** RDP (TCP port 3389) did not appear in the scan results.

---

## Step 2 — Manual Verification on Target

Checked the Windows 7 VM directly to investigate the absence of RDP in the scan.

- ✅ Remote Desktop was **allowed** in the firewall settings  
- ❌ The **Remote Desktop service was not running**

This explained why Nmap did not detect an open port — there was no active listener on port 3389.

---

## Root Cause

After further investigation, the root cause was identified:

> **Windows 7 Home Basic does not support inbound Remote Desktop connections.**

Microsoft restricts RDP host functionality to higher editions of Windows 7:

| Edition | RDP Host (Accept Connections) |
|---|---|
| Home Basic | ❌ Not supported |
| Home Premium | ❌ Not supported |
| Professional | ✅ Supported |
| Ultimate | ✅ Supported |

While the firewall setting for Remote Desktop existed in the UI, the underlying service (`TermService`) does not run on Home Basic, making the port unreachable regardless of firewall configuration.

---

## Resolution

To properly test RDP enumeration and exploitation, a **Windows 7 Professional** (or higher) VM is required.

**Next step:** Set up a Windows 7 Professional virtual machine as the target.

---

## Key Takeaways

- Always verify the **OS edition** when a service appears to be configured but is not reachable — licensing restrictions can silently prevent services from running.
- Nmap shows reality; if a port is closed, there may be a software/OS constraint rather than a misconfiguration.
- Home editions of Windows intentionally omit Remote Desktop *host* capability (clients can still initiate outbound RDP connections).

---

*Part of an ongoing series of security lab writeups.*
