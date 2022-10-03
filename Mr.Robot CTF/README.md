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




- [x] key3
