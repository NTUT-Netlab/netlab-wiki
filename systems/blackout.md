# 面對總務處停電的因應措施

停電就是要關機器，但是機器要關有一個「順序」，不然到時候不是關機器，而是觀落因...

復電之後開機就沒有特定的順序了，直接按下電源鍵，重要服務的VM我已經設定讓他自動開啟了，所以開機後只要等個幾分鐘讓他們啟動即可。

## 關機

關機順序就是 1)Ai 學習機、2)軟體路由器、3)嚴嚴主機，這樣。

1. 登入 [vCenter](https://172.30.0.110)
2. 如果登入看到這個頁面，選擇 **Host and CLusters**

![](/img/blackout/fig1.png)

3. 這邊可以看到三台 ESXi Server

![](/img/blackout/fig2.png)

4. 先把 AI 學習機關機，選擇`172.30.0.102`，按 **Actions** -> **Power** -> **Shut down**

![](/img/blackout/fig3.png)

5. 輸入關機原因，這邊可以亂打，然後按下 **OK** 之後它就會慢慢關機了

![](/img/blackout/fig4.png)

6. 再把軟體路由器關掉，選擇 `172.30.0.101`，並重複和上面一樣的關機動作

![](/img/blackout/fig5.png)

7. 最後，嚴嚴主機**一定要最後關**，因為 vCenter VM 是在這台運行的。(不小心先關掉的話你就上不了 vCenter 了，變成要一一登入每台的 ESXi 管理界面去關機。)

![](/img/blackout/fig6.png)

8. 停電時 UPS 會偵測到斷電而一直叫(UPS有兩台)，如果深夜停電沒有人在Lab是沒差，怕吵的話可以長按UPS的電源按鈕把他關掉。(長按電源按鈕時UPS也會有長音嗶 別怕[@@](https://www.youtube.com/watch?v=ljOjZ9vSMQE))

![](/img/blackout/fig7.png)

## 開機

開機沒有特定順序，全部按開就好...

## More details if you wanna dive deep

他老母，某次停電總務處沒發小郵差，結果是有人臨時看到科研樓下貼一張小小的公告，才知道當晚要停電，大家緊急遠端

這行政真的好棒棒

![](/img/blackout/fig8.jpg)

打電話過去指教一番，結果只會趁我們實驗室財產報廢的時候刁難拿翹；幾年前還把中正管旁邊的空地出租給人辦喪事，被靠北還回答「我們要尊重供**中國人**的傳統」，中你老妹，操!
