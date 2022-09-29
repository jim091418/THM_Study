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


- [x] What is the root.txt flag?
