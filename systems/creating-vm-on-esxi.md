# 透過 ESXi 管理界面建立 VM

1. 登入 ESXi 管理界面

![](/img/creating-vm-on-esxi/fig1.png)

2. 在VM安裝OS的ISO檔要自行準備並上傳到Storage，不過可以先看看Storage有沒有你需要的ISO (之前我有上傳一些ISO了) 
3. 欲查看儲存區有哪些ISO，點擊**Storage**

![](/img/creating-vm-on-esxi/fig2.png)

4. 這幾個儲存區，以**AI**開頭的就是AI學習機上的硬碟，而`FreeNAS`則是另一台FreeNAS的NFS網路磁碟，通常我喜歡將Image存在FreeNAS上，點選FreeNAS

![](/img/creating-vm-on-esxi/fig3.png)

5. **Datastore browser**

![](/img/creating-vm-on-esxi/fig4.png)

6. 如果沒有你要的ISO檔，請上傳你的ISO檔 （在建立VM前先確保ISO已上傳）
7. 上傳完畢後，選擇左側選單**Virtual Machine**

![](/img/creating-vm-on-esxi/fig6.png)

8. 點選**Create / Register VM**

![](/img/creating-vm-on-esxi/fig7.png)

9. **Create a new virtual machine** > **Next**

![](/img/creating-vm-on-esxi/fig8.jpg)

10. 為你的VM命名，如果是作為個人使用，請用學號末三碼作為開頭，ex. `017-ubuntu`

![](/img/creating-vm-on-esxi/fig9.jpg)

11. 接著來選擇儲存區，這是指VM的硬碟要放在哪個儲存區，建議這邊不要選FreeNAS，把VM的硬碟放在網路磁碟上，效能會比較差。

![](/img/creating-vm-on-esxi/fig10.png)

12. 接著是VM的設定，請注意 Hard Dick 設定要展開，確認 **Dick Provisioning** 必須是 **Thin Provision**，也就是**精簡佈建**。若不是，請更改此設定

![](/img/creating-vm-on-esxi/fig11.png)

13. 在光碟機掛載剛才上傳的ISO。選擇**Datastore ISO file**

![](/img/creating-vm-on-esxi/fig12.png)

14. 接著選擇你剛剛上傳的ISO檔

![](/img/creating-vm-on-esxi/fig13.png)

15. 如果在 AI學習機要使用顯卡的話，選擇 **Add other device** > **PCI Device**

![](/img/creating-vm-on-esxi/fig15.png)

16. 接著選擇顯示卡的型號

![](/img/creating-vm-on-esxi/fig16.png)

17. 一張顯示卡要新增兩個 PCI Device，一個是顯卡本身、另一個是**HDMI Audio Controller**，只新增其中一個會無法開機VM。注意兩個的PCI位置(?) 開頭要一樣，像圖片中就是 `0000:1a:00`開頭的，不然一樣無法開機

![](/img/creating-vm-on-esxi/fig17.png)

![](/img/creating-vm-on-esxi/fig18.png)

18.  確認設定沒問題後，**Next** > **Finish** 即可
19.  剛剛建立好的VM會是關機狀態，找到剛剛的VM，勾選並按**power on**

![](/img/creating-vm-on-esxi/fig14.png)

20. 接著就會進入 OS 的安裝程序了