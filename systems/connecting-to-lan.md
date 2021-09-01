# 連接進入 Netlab 系統 LAN

整個系統是以 pfsense 為中心分為 WAN 與 LAN，為了資安考量，許多管理界面(Portal) 只對 LAN 的網段 (`172.30.0.0/24`) 開放，因此管理者必須透過 VPN 或是其他方式進入 LAN 的網路。

以下有兩種方式：安裝 OpenVPN 或是連接實驗室的 Wifi (Netlab-Internal)

## OpenVPN

OpenVPN 的好處：在任何地方都可以連入 LAN，用起來對使用者很方便

### 安裝

到[這邊](https://github.com/NTUT-Netlab/confidential/releases/tag/Netlab)下載 Windows 安裝檔，檔名為`openvpn-pfSense-TCP4-8877-admin-install-2.5.2-I601-amd64.exe`，使用「下一步」安裝法即可。這個安裝檔內含設定檔，所以安裝完成後無須做設定，可直接使用。

### 連線

1. 安裝完成後，左下角找到這個 icon，右鍵!

    ![](/img/connecting-to-lan/fig1.png)

2. **pfSense-TCP4-8877-admin-config** > **連線**

    ![](/img/connecting-to-lan/fig2.png)

<!-- TODO: -->
3. 輸入帳號密碼，帳號密碼和登入 pfSense 管理界面一樣，請見[各種帳密]()

4. 等待連線建立，完成後視窗自動消失

    ![](/img/connecting-to-lan/fig3.png)

5. 接著就可以存取任何位於 `172.30.0.0/24` 的服務了，不用透過 proxy

## 使用 Netlab 的 Wifi Netlab-Internal

1. 找到 SSID 為 `Netlab-Internal` 的 Wifi
2. 密碼請見[各種帳密]() <!-- TODO: -->
3. 我讓這個 Wifi 不能連到 Internet，免得有人拿來上網，讓流量爆掉害IP被阻擋，導致外面的人看不到實驗室網頁 (要用 Wifi 上網請用另一個 Wifi `netlab` 或是學校 AP `NTUT-802.1x`)

## More details if you wanna dive deep

- [pfSense Configuration Recipes — OpenVPN Remote Access](https://docs.netgate.com/pfsense/en/latest/recipes/openvpn-ra.html)

