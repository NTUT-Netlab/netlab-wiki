# VMWare vSphere (ESXi 與 vCenter) 管理

ESXi 和 vSphere 是啥？這是網路上貼過來的，還不錯的敘述：

> VMware vSphere（VMware ESXi）是一種裸金屬架構的虛擬化技術。虛擬機器直接執行在系統硬體上，建立硬體全模擬例項，被稱為“裸機”，適用於多臺機器的虛擬化解決方案，而且可以圖形化操作。
>
> vSphere 是VMware公司推出一套伺服器虛擬化解決方案，vSphere 中的核心元件為 VMware ESXi（取代原ESX），ESXi是一款可以獨立安裝和執行在祼機上的系統，因此與他我們以往見過的VMware Workstation 軟體不同的是它不再依存於宿主作業系統之上。
>
> 在ESXi安裝好以後，我們可以通過~~vSphere Client 遠端連線~~ vCenter web UI 控制，在ESXi 伺服器上建立多個VM（虛擬機器），在為這些虛擬機器安裝好Linux /Windows Server 系統使之成為能提供各種網路應用服務的虛擬伺服器，ESXi 也是從核心級支援硬體虛擬化，運行於其中的虛擬伺服器在效能與穩定性上不亞於普通的硬體伺服器，而且更易於管理維護
>
> VMware ESXi是我們常說的ESXi主機，虛擬化層元件的作用：
>
> - ESXi用於協調物理計算機的資源，同時通過ESXi管理其上的虛擬機器，如部署、遷移等操作。
> - 同時還可以通過ESXi對物理計算機上的網路儲存資源僅從管理，ESXi通過配置虛擬交換機上的vSwitch管理配置網路資源，通過VMfs和nfs管理虛擬儲存資源。

這邊我補充說明，vSphere就像是Office一樣，指的是一系列的軟體，裡面包含 ESXi 和 vCenter。**ESXi 是一個 Hypervisor，而 vCenter 是一個管理監控平台**。

實驗室的架構由三台運行 ESXi 的實體主機 (嚴嚴主機、軟體路由器和Ai學習機)，這三台受到 vCenter 的管理，然而 vCenter 這個平台我讓他以VM的形式運行在嚴嚴主機裡面。即使 vCenter 沒有啟動，各個ESXi主機上的VM會照常運行，也可以個別登入ESXi的管理界面去對不同主機做設定。

用 vCenter 可以一次看到這三台ESXi的VM和設定，不用一台一台的登入ESXi管理界面，所以我建議一般管理VM還是用vCenter，除非vCenter 這個VM或是嚴嚴主機crash了(沒遇過0.0)才去登入個別的ESXi。

## 登入管理界面

[實驗室網路架構-LAN IP 位址對照](https://github.com/NTUT-Netlab/netlab-wiki/blob/main/systems/network-structure.md#lan-ip-%E4%BD%8D%E5%9D%80%E5%B0%8D%E7%85%A7)可以查看三台ESXi管理界面以及vCenter管理界面的IP。密碼都在[各種帳密-系統管理者專區](https://github.com/NTUT-Netlab/confidential/blob/main/passwd.md#%E7%B3%BB%E7%B5%B1%E7%AE%A1%E7%90%86%E8%80%85%E5%B0%88%E5%8D%80)。

## VM 分佈圖

可以對照[實驗室網路架構圖](https://github.com/NTUT-Netlab/netlab-wiki/blob/main/systems/network-structure.md#%E5%AF%A6%E9%A9%97%E5%AE%A4%E7%B6%B2%E8%B7%AF%E6%9E%B6%E6%A7%8B%E5%9C%96)看

![](/img/esxi-and-vsphere/fig1.jpg)

## VM 命名規則

只要 VM 名稱的開頭是以 `service-` 開頭，代表是重要服務，可以對照上面分佈圖的VM。
`pfsense` 就是 pfSense 的 VM，請多加關照這台VM的狀況，畢竟是整個架構的核心。

如果是自己做Lab用的VM，將學號末三碼作為VM名稱前綴，例如`017-EVE-NG`。

![](/img/esxi-and-vsphere/fig2.png)


## More details if you wanna dive deep

- [IT新手建置看這裡　vSphere 6.7從無到有](https://www.netadmin.com.tw/netadmin/zh-tw/feature/0F96E1DDBD5F4C11BDA57147386662B4)
- [VMware vSphere 6.7私有雲建置實戰 / 顧武雄著](https://libholding.ntut.edu.tw/bookDetail.do?id=510762)
