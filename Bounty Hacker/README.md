h4cked
===

### environment
KALI Linux ip 10-10-144-240

TARGET 10.10.215.4
###  Oh no! We've been hacked!

- [x] Who wrote the task list? 
FTP用anonymous登入即可獲得檔案
- [x] What service can you bruteforce with the text file found?
ssh，nmap只有ftp、ssh、http
- [x] What is the users password? 
剛剛FTP中獲取LIN跟一個LOCK.TXT，LOCK.TXT當字典用HYDRA破解即可
- [x] user.txt
登入後CAT user.txt
- [x] root.txt
sudo -l可以看到tar有root權限，在google搜尋privilege tar root，即有相關方法，得到後cat /root/root.txt即可
![image](https://user-images.githubusercontent.com/67756786/192958374-5e935824-cc6b-4ab5-95ad-a25ffc4c0f18.png)
