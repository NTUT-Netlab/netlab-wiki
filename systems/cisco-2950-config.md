# Cisco 2950 交換器設定

[Cisco 2950 設定備份](https://github.com/NTUT-Netlab/confidential/blob/main/cisco-2950-config.config)

**交換器我都設定好了，平時不太需要更動他**，但是如果要拿新設備接到這個網路裡，最好確認一下2950上面界面的設定。

很抱歉了學弟妹，實驗室只有多餘的 Cisco 2950 可以使用，而這是 10 幾年前的型號，要遠端登入只能用 telnet，不支援SSH，每個連接埠只有 100 Mbps，QQ

不過就將就用吧，反正使用量也不會大到哪去...

換個角度想，如果你沒有操作過任何廠牌的交換器，也沒聽過CCNA證照，這是一個動手操作的好機會。想學習 Cisco 交換器的指令和一些Layer 2網路技術等等，蠻推薦看一下 CCNA 證照的內容(不一定要去考啦，報名費超貴)，底下有父一本電子書，其實大部份的操作都寫在裡面，我也懶的在這邊寫太多。

連線進入2950有兩種方式，一種是透過telnet，另一種是用 Console 線插到 2950背後寫著Console的接孔，然後另一端接 RS232 轉接至 USB，再連接到電腦上。電腦上可以用putty或是我比較愛用的SecureCRT來讀取COM port的訊號，對設備進行設定。

## 線路編號與 Port 對應

就是 [實驗室網路架構](https://github.com/NTUT-Netlab/netlab-wiki/blob/main/systems/network-structure.md) 中的線路編號，和 Switch 的界面編號對應，寫在這邊免得拔掉忘記怎麼接回去

| 線路編號 | 2950 界面名稱   |
| -------- | --------------- |
| 1        | FastEthernet0/1 |
| 2        | FastEthernet0/2 |
| 3        | FastEthernet0/3 |
| 4        | FastEthernet0/4 |
| 5        | FastEthernet0/5 |
| 24        | FastEthernet0/24 |

其實線路編號就是照interface編號去標的，方便吧

## VLAN 設定

全部用 VLAN 10，但是因為我都用 `switchport mode access`，所以你設備不用管他，Access的意思就是那個 802.1Q VLAN tag 到Switch interace 出去時就會被解掉了，所以伺服器不用設VLAN就可以連。

## 使用 telnet 登入 2950 與基本操作

在你的 linux 用 telnet

```sh
telnet 172.30.0.254
```

接著會看到

```
Trying 172.30.0.254...
Connected to 172.30.0.254.
Escape character is '^]'. 


User Access Verification

Password:
```
這邊輸入telnet密碼 (見[各種帳密-系統管理者專區](https://github.com/NTUT-Netlab/confidential/blob/main/passwd.md#%E7%B3%BB%E7%B5%B1%E7%AE%A1%E7%90%86%E8%80%85%E5%B0%88%E5%8D%80))，輸入完會進到一個叫 **user exec mode** 的模式，以 `>` 符號開頭，這個模式幾乎啥都不能做，要更改設定必須進入**特權模式** (**priviledged exec mode**)，輸入 `enable` 指令就可以進入，是以`#`開頭。這邊還需要另一個密碼，叫**enable password**，密碼一樣去老地方找。
```
netlab-internal-sw>enable
Password: 
netlab-internal-sw#
```
接著這邊就可以查看設定和一些Switch界面資訊，幾個常用的像是
- `sh run` 查看運行中的設定
- `sh start` 查看儲存在設備設定(開機時會讀取)
- `sh ip int br` 看界面狀態
- `sh int` 看界面詳細狀態
- `sh vl br` 看VLAN以及interface對應
- `wr mem` 或`copy run start` 除存設定(不然下次重開機變更會不見)

To configure from a CLI, you can make global changes to the router by typing `configure terminal`
or just `config t`. This will get you into **global configuration mode** where you can make changes to
the running-config.

懶的翻譯，就像醬子
```
netlab-internal-sw#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
netlab-internal-sw(config)#
```

到這邊才可以改設定，會是以`(config)#`開頭的模式

之後怎麼改設定，不是我應該教的，請自己看下面的書，辛苦了。

## More details if you wanna dive deep
- [CCNA Routing and Switching Complete Study Guide by Todd Lammle](https://www.yottabytetechnologies.com/Todd%20Lammle%20-%20CCNA%20Routing%20and%20Switching%20Complete%20Study%20Guide.pdf) (Chapter 6 就是Cisco 設備基本操作)