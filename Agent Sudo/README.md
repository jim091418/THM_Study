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

進入目錄後，發現8702.ZIP，解壓縮需要使用PASSWORD，解鎖ZIP PASSWORD很簡單，先使用zip2jhon取hash後再使用john破解即可
```

```

- [x] steg password


```
```

- [x] Who is the other agent (in full name)?


```
```

- [x] SSH password

### Capture the user flag
### Privilege escalation
