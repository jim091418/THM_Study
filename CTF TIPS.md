CTF TIPS
===

## SCAN
### Nmap scan
  - Parameters  
    `-sV` Probe open ports to determine service/version info  
    `-sC` equivalent to --script=default  
    `--script` select a specify script  
    
    - **http-enum** web path scan  
    - **vuln** scan known vulnerability  
    
    `-P-` scan all ports  
    `-p 1-1024` scan port 1-1024 port  
    If not specify port, it will only scan common port.(e.g ssh/22 telnet/23...etc)  
    
## REVERSE SHELL
### Python PTY
  1. `python3 -c 'import pty; pty.spawn("/bin/bash")'`  
  2. press same time `Ctrl + z`  
  and should output `[1]+ Stopped nc -nvlp 8888`  
  3. `stty raw -echo; fg`
  4. and press twice `Enter`  
### PHP Reverse
  PHP Reverse shell(https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)
 
## PRIVILEGE ESCALATION
### GTFBins

WEB(https://gtfobins.github.io/)
    
