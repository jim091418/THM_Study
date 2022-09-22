Agent Sudo
===
### environment
KALI Linux 10.10.128.188

TARGET 10.10.231.25
### Enumerate

- [x] How many open ports?

有多少開放PORT，直接使用NMAP來掃PORT即可得出答案，可從圖片確認有21、22、80

```
nmap -A 10.10.231.25
```

![image](https://user-images.githubusercontent.com/67756786/191733772-deab86e8-7db8-46e7-b19a-f0a117e4f629.png)

- [x] How you redirect yourself to a secret page?

看到80Port習慣先用nmap確認一下http是否有藏甚麼資訊，在圖片中沒看到有用的資訊QQ

```
nmap -p80 --script http-enum 10.10.231.25
```

![image](https://user-images.githubusercontent.com/67756786/191734198-d606f93c-8efc-41b4-8169-2c9660b11190.png)

接著直接上網站確認，網站上面說使用自己的User-Agent來登入此網站，可以使用curl -A 來偽造User-Agent

![image](https://user-images.githubusercontent.com/67756786/191734391-73d49f57-4292-45c5-a704-e987bbb46949.png)

- [x] What is the agent name?

從第二題題目似乎有需要redirect+User-agent，用help來確認能有甚麼指令

![image](https://user-images.githubusercontent.com/67756786/191735180-83d0e05b-f30b-40d5-b4ae-8f65dd18dd91.png)

我們確認網站可以使用target靶機ip，user-agent要使用他們的代號，可以從剛剛確認他們有一個代號R的，首先帶入!從圖片第一句
當中可以發現他們應該是使用A-Z來做代號，接著就是是看A-Z

```
curl -A "R" -L 10.10.231.25
```

![image](https://user-images.githubusercontent.com/67756786/191735580-12b5f2dd-23cb-4c4b-8658-e24e093811f6.png)

測到C的時候發現R寫給Chris(Agent C)的信，R叫C趕快跟J說換掉他的破密碼

![image](https://user-images.githubusercontent.com/67756786/191736011-58dad295-7e4c-460f-9d7c-97112ac79a6b.png)



### Hash cracking and brute-force

- [x] FTP password

這邊題目直接說brute-force了，那就可以直接暴力破解，user就直接用上面所知道的chris，密碼字典檔就使用kali自帶的rockyou.txt

```
hydra -l chris -P /usr/share/wordlists/rockyou.txt ftp://10.10.231.25
```

![image](https://user-images.githubusercontent.com/67756786/191737409-cd62334a-b2aa-47cb-ae72-9c2fad6a7584.png)


- [x] Zip file password

登入ftp

```
ftp -4 10.10.231.25
```

裡面有3個檔案
![image](https://user-images.githubusercontent.com/67756786/191737778-9e06c5b0-a2df-4243-ae96-33e7fd616f33.png)

全都下載下來，並離開
```
mget *
```
![image](https://user-images.githubusercontent.com/67756786/191738013-c9b2bd03-df0e-4315-898d-4442519ed0e6.png)

但是題目說會需要zip password+steg password，有可能將檔案一起合併在圖片當中了請參考(https://ithelp.ithome.com.tw/m/articles/10278964)

使用binwalk來確認架構+分解

```
binwalk cutie.png
binwalk -e cutie.png
```

![image](https://user-images.githubusercontent.com/67756786/191739146-f0ca6795-5f5f-4c98-97c5-6f65578768a8.png)

進入目錄後，發現8702.ZIP，解壓縮需要使用PASSWORD，解鎖ZIP PASSWORD很簡單，先使用zip2jhon取hash後再使用john破解即可，可發現密碼是alien

```
zip2john 8072.zip > pw.hash
john pw.hash
```

![image](https://user-images.githubusercontent.com/67756786/191780722-c23229d3-e4b1-4868-aa4c-ea1229ce8719.png)

接著解壓所檔案，查看txt檔案，獲取QXJlYTUx字串

```
7z e 8702.zip
cat To_agentR.txt
```
![image](https://user-images.githubusercontent.com/67756786/191791323-70cb3639-fc4d-47ab-9545-516274e9d15a.png)



- [x] steg password

剛剛知道有獲取一串字串，可以將它丟到(https://gchq.github.io/CyberChef/)，會自動判定他有可能是甚麼加密方式，並且自動幫你解密，之後獲得
新字串Area51

![image](https://user-images.githubusercontent.com/67756786/191793059-606d14df-d864-4e7e-a1e1-892d1d2ccd7a.png)

因題目問steg password，因此利用此字串來解密另一張照片

```
steghide extract -sf cute-alien.jpg -p Area51
```

![image](https://user-images.githubusercontent.com/67756786/191794847-11f2edf8-9bab-4315-845a-7e9a993bdea3.png)


- [x] Who is the other agent (in full name)?

![image](https://user-images.githubusercontent.com/67756786/191795068-6bc4ab70-49b8-4ddf-91dc-35cb7dedb7bd.png)


- [x] SSH password
![image](https://user-images.githubusercontent.com/67756786/191795093-9ca85144-5c2c-475e-8d78-1402ecc3a231.png)


### Capture the user flag
- [x] What is the user flag?

連進去ssh

```
ssh james@ip
```

![image](https://user-images.githubusercontent.com/67756786/191795488-0b1ec176-48c9-4e44-9570-d9767bce21be.png)

- [ ] What is the incident of the photo called?
### Privilege escalation
- [x] CVE number for the escalation 
- [x] What is the root flag?
- [x] (Bonus) Who is Agent R?
