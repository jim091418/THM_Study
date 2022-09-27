h4cked
===

### environment
KALI Linux ip 10.10.136.98

TARGET 10.10.194.25
###  Oh no! We've been hacked!

- [x] The attacker is trying to log into a specific service. What service is this?

隨便往下滑，即可知道在FTP-Brute-Force

![image](https://user-images.githubusercontent.com/67756786/192454846-dfa19b47-a439-40c8-b43a-15a5387f84f8.png)

- [x] There is a very popular tool by Van Hauser which can be used to brute force a series of services. What is the name of this tool?

Hydra，這題直接直覺猜的，因為5碼還有基本上都是用這工具xD

- [x] The attacker is trying to log on with a specific username. What is the username?

往下滑及有答案

![image](https://user-images.githubusercontent.com/67756786/192454991-e758dc3e-c9c2-4161-87ad-dbf8c62e74d5.png)


- [x] What is the user's password?

找到成功登入封包 Follow -> TCP Stream

![image](https://user-images.githubusercontent.com/67756786/192455214-41820f92-0055-45f8-891b-e8d984ccf363.png)


- [x] What is the current FTP working directory after the attacker logged in?

往下找到有執行指令的地方 Follow -> TCP Stream

![image](https://user-images.githubusercontent.com/67756786/192455489-3d90e70e-3e58-44f6-b32e-16a0f5c9fd7e.png)


- [x] The attacker uploaded a backdoor. What is the backdoor's filename?

![image](https://user-images.githubusercontent.com/67756786/192455762-418c2554-184f-4f0f-9125-da82ecae9812.png)


- [x] The backdoor can be downloaded from a specific URL, as it is located inside the uploaded file. What is the full URL?

從 FTP-DATA中查看 Follow -> TCP Stream

![image](https://user-images.githubusercontent.com/67756786/192456134-b9f23bc7-e0e4-4eed-bfba-96796d5762ec.png)

- [x] Which command did the attacker manually execute after getting a reverse shell?

從GET SHELL.PHP下面幾個封包點選Follow -> TCP Stram

![image](https://user-images.githubusercontent.com/67756786/192456374-3222f767-6286-4faa-9522-c4f266feb317.png)


- [x] What is the computer's hostname?

![image](https://user-images.githubusercontent.com/67756786/192456516-3fa97b38-360b-4fdf-ad5b-5a9d3547e040.png)


- [x] Which command did the attacker execute to spawn a new TTY shell?

基本上進入後都先執行這個，不然原本的很難用

![image](https://user-images.githubusercontent.com/67756786/192456563-e12ae8ec-80a0-44b9-b3f6-091fd8b2f8be.png)


- [x] Which command was executed to gain a root shell?

確認自己有root權限後直接root

![image](https://user-images.githubusercontent.com/67756786/192456723-2abbabce-d16a-4e0d-9733-255c3801ccf0.png)


- [x] The attacker downloaded something from GitHub. What is the name of the GitHub project?

![image](https://user-images.githubusercontent.com/67756786/192456812-945201b3-157a-4672-8f5a-f09cb326dc58.png)


- [x] The project can be used to install a stealthy backdoor on the system. It can be very hard to detect. What is this type of backdoor called?

github上有介紹 rootkit

###  Oh no! We've been hacked!
- [x] Run Hydra (or any similar tool) on the FTP service. The attacker might not have chosen a complex password. You might get lucky if you use a common word list.

111

- [x] Change the necessary values inside the web shell and upload it to the webserver
- [x] Create a listener on the designated port on your attacker machine. Execute the web shell by visiting the .php file on the targeted web server.
- [x] Become root!
- [x] Read the flag.txt file inside the Reptile directory
