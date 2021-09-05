# 實驗室網路架構

## 實驗室網路架構圖
![](/img/network-structure/fig1.png)

[使用 Draw.io 編輯圖片](https://drive.google.com/file/d/1-IVHR8j2f1Z5ti-iXY9qrPhisW9qyKA1/view?usp=sharing)

## 線路編號

每個線路兩端都有線路編號，對應到架構圖中的方框數字。

三位數的編號是和學校的 Switch 對接的線路，直連校園網路。

![](/img/network-structure/fig2.jpg)

![](/img/network-structure/fig3.jpg)

## LAN 和 WAN

這架構簡單分為 LAN 和 WAN，由 pfsense 作為路由器與防火牆。LAN 是 `172.30.0.0/24`  的 Subnet，Gateway 是 pfsense 的 IP `172.30.0.1`，在 LAN 的機器需將 Gateway 指到 pfsense 並透過 pfsense NAT 出去到外部網路。所有實驗室的服務 (網頁、FreeNAS、Gitlab、ESXi等等) 都是位在 LAN，由 pfsense 的防火牆規則控制外部連接。

WAN 則是指學校的 `140.124` 開頭 IP 的網段，在14樓是屬於 `140.124.182.0/24` 的網段，Gateway 在 `140.124.182.254`。實驗室所有人的電腦都是直接連在這個網路上，網路線會接到實驗室前面的兩台 Cisco 2960 交換器，那是屬於計中的管轄範圍。

pfsense 有兩個 WAN 的 IP。一個是 `140.124.182.213`，這個 IP 可以透過 DNS 解析 `netlab.csie.ntut.edu.tw` 這個 domain 獲得，而這個 DNS 紀錄是學校在維護，不容易修改。另外，避免這個 IP 超過流量限制 (想像這個 IP 背後有那麼多機器和VM要NAT出去)，所以我設定另一個 IP `140.124.182.184` 作為 NAT 出去的 Source IP。而提供實驗室同學使用的掃描機PC和NAS都是透過 pfsense port forward 的方式，統一用 `140.124.182.184` 存取，而且只限 `140.124.182.0/24` 內的 IP 連線，從學校網路以外的地方連線請先使用[學校的VPN](https://vpn03.ntut.edu.tw)再遠端連接這些服務。

## LAN IP 位址對照

| IP                      | 用途                                | 說明                                                                                                                                                             |
| ----------------------- | ----------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 172.30.0.1              | [**VM**] pfSense                    | pfSense 的管理界面和 Default Gateway                                                                                                                             |
| 172.30.0.2              | [**VM**] Gitlab                     | 存放程式碼，包含實驗室網站的程式碼以及 CI/CD Pipeline                                                                                                            |
| 172.30.0.3              | [**VM**] FreeNAS                    | FreeNAS 是開源的網路儲存伺服器(就NAS啦)，可作Software RAID，存放歷年來的Group Meeting和口試的影片/投影片、考古題以及其他有用的東西，是讓實驗室同學共享檔案的地方 |
| 172.30.0.4              | 管理用Wifi AP                       | 讓管理者透過連接這個Wifi進入LAN網段，也可以用 OpenVPN 達到一樣的目的                                                                                             |
| 172.30.0.10             | 掃描機 PC                           | 這台PC連著掃描機，任何同學都可以用RDP遠端到這台機器掃描檔案，RDP是透過pfsense轉發的                                                                              |
| 172.30.0.11             | [**VM**] 實驗室Web Server (Active)  | 實驗室網站的 Nginx Server，外部透過 pfsense 的 HAProxy 存取，這台是 Active，除非這台掛掉流量才會導向到下面的 Standby                                             |
| 172.30.0.12             | [**VM**] 實驗室Web Server (Standby) | 實驗室網站的 Nginx Server，外部透過 pfsense 的 HAProxy 存取，這台是 Standby 做備援，除非Active掛掉流量才會導向到這台 Standby                                     |
| 172.30.0.30~172.30.0.90 | DHCP 區間                           | 在 LAN 任何設備如果沒有設定固定IP（包含VM），會拿到這個區間的IP。DHCP Server 在 pfSense上，不建議重要的服務主機用 DHCP，為什麼應該不用我說吧                     |
| 172.30.0.100            | ESXi 管理IP                         | 這是架構圖中的「嚴嚴主機」的ESXi 管理IP，這台ESXi受到 vSphere 的管理，可以直接登入 vSphere 網頁管理整個叢集，而不用透過這個 IP 的ESXi管理界面管理。              |
| 172.30.0.101            | ESXi 管理IP                         | 這是架構圖中的「軟體路由器」的ESXi 管理IP，這台ESXi受到 vSphere 的管理，可以直接登入 vSphere 網頁管理整個叢集，而不用透過這個 IP 的ESXi管理界面管理。            |
| 172.30.0.102            | ESXi 管理IP                         | 這是架構圖中的「AI學習機」的ESXi 管理IP，這台ESXi受到 vSphere 的管理，可以直接登入 vSphere 網頁管理整個叢集，而不用透過這個 IP 的ESXi管理界面管理。              |
| 172.30.0.110            | [**VM**] vSphere                    | 這個 VM 是一個 VMWare 的 ESXi 叢集管理系統，可以管理多台裝有 ESXi Hypervisor 的伺服器，登入這個 IP 的網頁就可以管理所有架構中所有主機上的 VM                     |
| 172.30.254              | Cisco 2950 Switch 管理 IP           | Layer 2 的 Switch 有 IP?? 別誤會，這只是讓你遠端登入 Switch 做設定的管理IP                                                                                       |

## WAN 位址對照

WAN 的 IP 就是屬於學校的 IP，資安考量不會在這裡公開，畢竟計中那邊的防火牆是佛系擋法。

請見另一個 Private 專案的 [WAN 位址對照](https://github.com/NTUT-Netlab/confidential/blob/main/ntut-wan-ip-addresses.md)

## More details if you wanna dive deep

- 架構想法參考自Redhat的[小飛機](https://facebook.com/paulintoro)大大的文章
  - [2019 大改造宅宅雲架構系列文-1 總綱及裝備清單](https://blog.pichuang.com.tw/20191114-homelcloud-high-level-design-1/)
  - [2019 大改造宅宅雲架構系列文-2 宅宅雲硬體介紹](https://blog.pichuang.com.tw/20191115-homelcloud-high-level-design-2/)
  - [2019 大改造宅宅雲架構系列文-3 宅宅雲網路架構設計](https://blog.pichuang.com.tw/20191116-homelcloud-high-level-design-3/)
  - [2019 大改造宅宅雲架構系列文-4 pfSense](https://blog.pichuang.com.tw/20191116-homelcloud-high-level-design-4/)
