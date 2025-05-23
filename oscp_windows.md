# 🪟 OSCP+ Windows Standalone Cheatsheet

## 1. 🔍 Initial Enumeration (Before Exploit)

### 🔎 Nmap
```bash
nmap -sC -sV -Pn -p- <IP> -oN win_full.nmap
```

### 🔑 Common Ports
| Port | Service | Notes |
|------|---------|-------|
| 135  | RPC     | Use `rpcclient` |
| 139, 445 | SMB | Null sessions, shares |
| 3389 | RDP     | Brute-force weak creds |
| 5985 | WinRM   | PowerShell remoting |
| 496xx | High RPC ports | Seen in newer Windows |

### 🗂 SMB
```bash
smbclient -L \\\\<IP>\\ -N
smbclient \\\\<IP>\\share -N
enum4linux-ng -A <IP>
```
