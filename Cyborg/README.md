Cyborg
===

### environment
KALI Linux ip 10.10.144.240

TARGET 10.10.221.58
###  Compromise the System

- [x] Scan the machine, how many ports are open?

nmap起手

```
nmap -sV 10.10.221.58
```

![image](https://user-images.githubusercontent.com/67756786/192938555-f8a012f3-3240-401b-80f6-c31bfe0ecf34.png)

- [x] What service is running on port 22?
如圖ssh
- [x] What service is running on port 80?
如圖http
- [x] What is the user.txt flag?

先用nmap掃是否有其他網頁

```
nmap -p80 --script http-enum 10.10.221.58 
```

![image](https://user-images.githubusercontent.com/67756786/192938829-a1357ec2-5b47-4c3e-a9cc-64e77af51c8e.png)

可以先從/admin開始看，裡面可以找到以下資訊，主要是說這個管理員掛不起來squid proxy，然後說music_archive他很確定很安全，因此
music_archive應該是滿重要的東西

![image](https://user-images.githubusercontent.com/67756786/192939068-a4079ffe-019b-42ea-aa50-cc7274c07e99.png)

從/etc看裡面有一個passwdd，然後有看到music_archive這關鍵字，後面是一段hash

![image](https://user-images.githubusercontent.com/67756786/192939306-0d0fc9ae-b790-4ede-b371-d1d60083497a.png)

![image](https://user-images.githubusercontent.com/67756786/192939350-8cb42a01-c6bf-455d-81ae-bac2a91da960.png)

先使用hash-identifier確認加密方式(https://github.com/blackploit/hash-identifier)

![image](https://user-images.githubusercontent.com/67756786/192939565-ce5dc2ad-f8d9-49fd-a3ed-3e19c02c3052.png)

等等會使用hashcat密碼破解，需先確認md5(APR)代號是多少(https://hashcat.net/wiki/doku.php?id=example_hashes) 

![image](https://user-images.githubusercontent.com/67756786/192939747-6d4a1a11-11f0-4049-b388-08667bd01d0f.png)

先將剛剛的hash存成hash.txt，如果沒注意到crack的密碼，密碼會存在~/.hashcat/hashcat.potfile裡面

```
hashcat --force -a 0 -m 1600 hash.txt /usr/share/wordlists/rockyou.txt
```
![image](https://user-images.githubusercontent.com/67756786/192940131-1ed72700-0ed8-459f-81f8-981ef9cf5c85.png)

總結一下目前獲得資訊music_archive squidward

接著從他admin網頁可以下載一個檔案，解壓縮後裡面有這些檔案

![image](https://user-images.githubusercontent.com/67756786/192949291-33b917c0-ae5d-4e9e-9791-37c37c1ded40.png)

README說這是一個borg backup檔案

![image](https://user-images.githubusercontent.com/67756786/192949320-10bbd94b-c6df-483d-98fa-774fe48942fe.png)

borg指令可參考(https://www.jibing57.com/2019/09/24/backup-tools-borgbackup/) ，目前基本上需要確認這備份檔案是否有資料(密碼為剛剛找到的)

```
borg list .
```

![image](https://user-images.githubusercontent.com/67756786/192949600-79f376a3-fe78-4128-aae0-7fe5bfc7652b.png)

確認有資料後要把備份檔案萃取出來

```
mkdir backup
cd backup
borg extract --list ../::music_archive
```
接著就可以開始搜尋資訊，我本來是想先找幾個沒找到資訊就用find，但是運氣很好第一次就找到，在Documents當中有note.txt

![image](https://user-images.githubusercontent.com/67756786/192950501-9a7144d5-d2b5-441d-91b9-deb0e4c730b0.png)

接著ssh即可找到flag

![image](https://user-images.githubusercontent.com/67756786/192950627-356a22f1-cbdb-488e-9a10-f8e8337949c9.png)


- [x] What is the root.txt flag?

首先看權限

```
sudo -l
```

![image](https://user-images.githubusercontent.com/67756786/192950676-bf522561-9720-4c46-b833-16aefa299db5.png)

在/etc/mp3backups/backup.sh有全部權限，接著看一下backup.sh是否可以利用

可以看到如果使用-c 後會把它紀錄到command這裡，在最後會echo，cmd結果(當初這邊有卡一下，可以去找getopts用法)

![image](https://user-images.githubusercontent.com/67756786/192950845-0e2a5d30-e4ee-458e-a7a5-a6c7b4c005e1.png)

![image](https://user-images.githubusercontent.com/67756786/192950911-6fb6d505-0991-46d8-8aa1-fddcf7d3d461.png)

知道這個就很好利用了，指令如下，即可獲取flag

```
sudo /etc/mp3backups/backup.sh -c "cat /root/root.txt"
```
![image](https://user-images.githubusercontent.com/67756786/192951167-7f14e020-e496-4243-b092-e1f7238b3b44.png)

