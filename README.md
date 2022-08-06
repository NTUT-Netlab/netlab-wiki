
<h1 align="center">
  <br>
  <img src="/img/logo.png" alt="Netlab" width="200">
  <br>
  Netlab Wiki
  <br>
</h1>

| 2022/08/06 更新：由於現實面的各種限制以及實驗室的人數減少，已經沒有成本維護這個架構，因此完全棄用。非常感謝大家這兩年的幫忙，這份文件會保留作為紀念，而且放 Github 上又不用錢 |
| --- |

<p align="center">
    2021年開始紀錄關於 Netlab 的各種文件，特別是實驗室的內部網路系統以及交接文件等等。<br>請實驗室幫忙管理的成員一起協助精進這份 Wiki 並持續傳承給下一屆。
</p>

<p align="center">
  <a href="#前言">前言</a> •
  <a href="#原則">原則</a> •
  <a href="#系統網路">系統網路</a> •
  <a href="#實驗室網頁開發">實驗室網頁開發</a> •
  <a href="#credits">交接</a> •
  <a href="#其他注意事項">其他注意事項</a> •
  <a href="#netlab-admins">Netlab Admins</a>
</p>

## 前言

關於實驗室的各種系統和網路架構，是我們 108 這一屆才開始架設的，一方面提高可靠度，另一方面可以練功。

包含自己架設的 NAS、RAID、Gitlab、ESXi、pfsense 軟路由等等，以應付同學檔案共享和存取公用掃描機的需求，以及 Lab 網頁 Hosting 的功能。在 108 年以前，這些東西全部 **集中在一台 Win7 Core 2 duo 的 PC 上面**，難以理解過去學長姐怎魔能容忍這樣使用。我有一些在交大、中央的朋友，都聽說他們的實驗室有自己架設的系統，有的甚至連 Active Directory 和 DNS 都有，總不能承認北科比較遜吧？不行，所以捲起袖子來做嘛!

為了讓未來接手管理的學弟妹可以上手維護這些東西，特此寫了這個 Wiki。當然 Netlab Wiki 不限於紀錄技術面的文件，也可以紀錄在學校要注意的事情 (例如口試和論文繳交流程、畢業離校流程，有些地方學校沒寫清楚)。希望學長偶爾來看看 Netlab Wiki 時可以發現學弟妹的 Contribution，改錯字也好，或是有地方寫錯了，或是你覺得有更好的架構要套用，都非常歡迎～

## 原則

1. 任何看似多餘的設定，其實都是必要的，平常請勿更改明明運行正常的設定，Unless you know what you're doing.
2. 作為 Admin 的責任，就是 Linux 裡面第一次輸入`sudo`時的提示訊息：
   - #1) Respect the privacy of others.
   - #2) Think before you type.
   - #3) With great power comes great responsibility.
3. Be stronger than your strongest excuse.
   - 有時可能會覺得：我學這麼多東西幹麻...之類的，別讓找藉口的力氣，勝過學習的動力。
4. 你一定要會 Linux 的常用指令
   - 在 Wiki 裡面的指令都是以 Linux 為主，你可以在 Windows 開 WSL 練習，然後我使用的 Ubuntu 都是 headless server，沒有 GUI，請學會敲指令。
5. Have backbone, disagree and commit.
   - 若有錯誤或是不足的地方，請動手改善。
6. Dive deep
   - 我在每個文件後面都會有 Dive deep 區塊，如果想了解更多細節，別害怕就直接 dive deep 吧，工程師的價值就是這麼來的。

## 系統網路

Work in progress

- 給新進實驗室的人看的文件
  - [Netlab Guides for Newcomers](general/guides-4-newcomer.md)

- 日常維運(例行公事)
  - [面對總務處停電的因應措施](systems/blackout.md)
  - [Netlab 網頁 TLS 憑證更新](systems/renew-ca.md) (90天一次)
  - 更換快過期的綠色乖乖

- 網路管理
  - [連接進入 Netlab 系統 LAN](systems/connecting-to-lan.md)
  - [實驗室網路架構](systems/network-structure.md)
  - [Cisco 2950 交換器設定](systems/cisco-2950-config.md)

- pfsense
  - [Netlab 網頁 TLS 憑證更新](systems/renew-ca.md)

- 系統管理(VMWare)
  - [VMWare vSphere (ESXi 與 vCenter) 管理](systems/vsphere.md)
  - [透過 ESXi 管理界面建立 VM](systems/creating-vm-on-esxi.md)

## 實驗室網頁開發
Work in progress

- [Netlab 網站開發文件](web/web-dev.md)

## 交接

Work in progress

## 其他注意事項

Work in progress

## Netlab Admins

Netlab Admins from past to present. ([emoji key](#emoji-key))

### 109
| [<img src="https://avatars.githubusercontent.com/u/37090553?v=4" width="100px;" alt="darknesshine "/><br /><sub><b>darknesshine </b></sub>](https://github.com/darknesshine)<br />👑 | [<img src="https://avatars.githubusercontent.com/u/25026452?v=4" width="100px;" alt="jim25522532"/><br /><sub><b>jim25522532</b></sub>](https://github.com/jim25522532)<br />⚙🌏 | [<img src="https://avatars.githubusercontent.com/u/11294412?v=4" width="100px;" alt="XinZhi"/><br /><sub><b>XinZhi</b></sub>](https://github.com/hellen6654)<br />⚙🌏 |
| :---: | :---: | :---: | 

### 108
| [<img src="https://avatars.githubusercontent.com/u/20513248?v=4" width="100px;" alt="htshen0221"/><br /><sub><b>htshen0221</b></sub>](https://github.com/htshen0221)<br /> 👑| [<img src="https://avatars.githubusercontent.com/u/30722178?v=4" width="100px;" alt="Li Yen Tseng \|\| Oscar"/><br /><sub><b>Li Yen Tseng \|\| Oscar</b></sub>](https://github.com/LYTzeng)<br />🥇⚙🌏 |
| :---: | :---: |

### Emoji Key

| Emoji |     代表意義     |
| :---: | :--------------: |
|   👑   | 實驗室總管(總務) |
|   🥇   |    系統架構者    |
|   ⚙   | 網路、系統管理者 |
|   🌏   |    網頁維護者    |
