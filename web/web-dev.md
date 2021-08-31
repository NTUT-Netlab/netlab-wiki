# Netlab 網站開發文件

## 重要資訊

- Netlab 的 domain：netlab.csie.ntut.edu.tw
- 網頁程式碼放在我們的自架 Gitlab 上：https://netlab.csie.ntut.edu.tw:8878/oscar/netlab
- 有透過 Gitlab CI 實現自動部屬的 CI/CD *(其實就一個靜態網站，我知道沒有 CI 哪來的 CD，但是這邊只做了 CD 別打我QQ)*
- 使用 [node 12.13.1](https://nodejs.org/download/release/v12.13.1/) + [yarn](https://yarnpkg.com/) v1.21.0  (node 12.13.1 是 Gitlab runner 容器裡的 node 版本，所以在本地端開發的環境請不要用相差太多的版本，除非你創一個新的 Gitlab runner 讓裡面有新版的 node，這比較麻煩不建議動它)
- 框架是使用 [Vuepress](https://vuepress.vuejs.org/) 1.2.0，是 Vue 框架的靜態網頁產生器，而且 Vuepress 支援用 Markdown 產生頁面，因此內容和前端排版是分開的，不用去尻 HTML
- [Markdown 語法說明](https://markdown.tw/) 將來要做工程師的，一定要熟 Markdown，從寫文件到作筆記都用 Markdown 效率會提昇，還在用 Word 寫 Document? 遜掉了喔，趕快 [用60秒學會Markdown](https://commonmark.org/help/) 變成有大大的樣子
- 憑證是掛在 pfsense 的 HAProxy 上，更新憑證請看 [Netlab 網頁 TLS 憑證更新](/systems/renew-ca.md)

## TL;DR

你不一定要架環境，只需要把repository clone 下來，用IDE打開然後直接抄過去的寫法增加/修改內容或頁面
然後再 `git push` 上去，等著網頁更新部屬就好。
沒有要深入研究的話，可以直接跳到 [開發步驟](#開發步驟) 閱讀。

## 架構跟 Pipeline

![](/img/web-dev/fig1.png)

- 我們使用兩台 VM 作為 web server，採用 Active-Standby 的 HA 架構，透過 pfsense 的 HAProxy 模組實現 Active-Standby 和 proxy 機制
- 當程式碼推送到 Gitlab 後，Gitlab 會自動 Build 靜態頁面檔案，並且自動將檔案覆蓋到這兩台 VM 上

| IP          | Role    | Nginx root directory |
| ----------- | ------- | -------------------- |
| 172.30.0.11 | Active  | /mnt/netlab-web/dist |
| 172.30.0.12 | Standby | /home/web/dist       |

## 檔案結構說明

```yml
.
├── .gitlab-ci.yml  # Gitlab pipeline 設定檔，盡量不要動它
├── .gitignore
├── package.json    # 這個不需要動
├── yarn.lock       # 這個不可以動
└── docs   # Vuepress 根目錄
    ├── .vuepress
    │   ├── dist    # 這裡是 build 完之後的靜態網頁檔案，已經被 gitignore 避免被 push 上去
    │   │   └── ...略
    │   ├── public  # 放置素材的地方 (圖、CSS、Group meeting 投影片等等)
    │   │   ├── images
    │   │   │   └── member # 首頁的實驗室成員照片
    │   │   │   │   └── 109 # 109 年入學同學的照片
    │   │   └── slides  # 歷年的 Group Meeting 投影片，年份以該「學年」開始的年份為資料夾名稱 (eg. 2021 7月以前是下學期，投影片應該放在 yesr2020)
    │   │       ├── ref
    │   │       ├── year2003
    │   │       ├── year2004
    │   │       ├── year2005
    │   │       ├── ...略
    │   │       └── year2020
    │   └── theme   # 網頁排版的地方
    │       ├── components  # 寫 Vue 的地方
    │       │    |── AlgoliaSearchBox.vue
    │       │    ├── DropdownLink.vue
    │       │    ├── DropdownTransition.vue
    │       │    ├── Equip.vue              # 首頁的元件：設備
    │       │    ├── Home.vue               # 首頁的元件：首頁大橫幅
    │       │    ├── Members.vue            # 首頁的元件：實驗室成員
    │       │    ├── NavLink.vue
    │       │    ├── NavLinks.vue
    │       │    ├── Navbar.vue
    │       │    ├── Page.vue
    │       │    ├── PageEdit.vue
    │       │    ├── PageNav.vue
    │       │    ├── Professor.vue          # 首頁的元件：指導教授
    │       │    ├── Proj.vue               # 首頁的元件：近期研究與產學計畫
    │       │    ├── ResearchTopics.vue     # 首頁的元件：近期研究主題
    │       │    ├── Sidebar.vue
    │       │    ├── SidebarButton.vue
    │       │    ├── SidebarGroup.vue
    │       │    ├── SidebarLink.vue
    │       │    └── SidebarLinks.vue
    │       ├── global-components
    │       ├── layouts
    │       │    ├── 404.vue    # 404的頁面，當然我是放404小隊的圖，不可以改掉，除非你整個專案砍掉去用React
    │       │    └── Layout.vue # 真正的首頁，將所有 components 組合起來
    │       ├── styles  # 放 Stylesheets 的地方，不過我都寫在 Vue 裡面
    │       └── util
    ├── Group Meeting   # 歷年 Group Meeting 頁面
    └── en              # 英文版網頁，底下的README.md是空的因為我把內容全寫在 Home.vue 裡面
```

## 開發步驟

1. 先確認你的 SSH public key 放到你的 Gitlab 帳號設定裡面，步驟在[這邊](https://docs.gitlab.com/ee/ssh/#generate-an-ssh-key-pair)

2. Clone 專案下來：
```sh
git clone ssh://git@netlab.csie.ntut.edu.tw:8879/oscar/netlab
cd netlab.csie.ntut.edu.tw
```

3. 稍候片刻，專案很大(因為有投影片，期望未來有勇者將投影片分離至別的Server來存取，而不是塞在專案裡)

4. 接下來分兩部份說明：常使用的[新增 Group meeting 資訊和投影片](#新增-group-meeting-資訊和投影片)以及不常用到的[修改首頁資訊](#修改首頁資訊)

### 新增 Group meeting 資訊和投影片

進入 Group meeting 資料夾
```sh
cd docs/Group\ Meeting/
```

**當下的學年的 Group Meeting資訊要放在 `README.md` 中**，舊的學年則用年份 (例如 `2019-2020.md`) 命名。

例如 2021 暑假到了，學年結束，所以我把目前的 `README.md` 重新命名為 `2020-2021.md` ，然後新建立一個 `README.md`。

```sh
mv README.md 2020-2021.md
touch README.md
```

如果建立新的 Markdown 檔案(也就是建立新頁面)，一開始一律加上下面這段：

```md
---
sidebar: true
lang: zh-TW
---
```

這個片段會被 Vuepress 讀取，並於這個頁面顯示 Sidebar，語系設為 zh-TW。

接著用 Markdown 建立表格，可以複製以前的格式來改，例如：

```md
| Date       | Topic                                  | Speaker              | Download                             |
| ---------- | -------------------------------------- | -------------------- | ------------------------------------ |
| 2020.10.21 | Dynamic Control Plane for SDN at Scale | Li-Yen Tseng(曾立嚴) | [download](/slides/year2020/xxx.pdf) |
```

前端就會出現這樣的表格

| Date       | Topic                                  | Speaker              | Download                             |
| ---------- | -------------------------------------- | -------------------- | ------------------------------------ |
| 2020.10.21 | Dynamic Control Plane for SDN at Scale | Li-Yen Tseng(曾立嚴) | [download](/slides/year2020/xxx.pdf) |

將報告同學的投影片新增到 `docs/.vuepress/public/slides/yearXXXX` 底下，如果是 110 學年度的投影片，就是 `docs/.vuepress/public/slides/year2021` 底下。

由於檔名會有空格以及特殊字元，這些字元在URL中需要被URL encode才能作為網址
所以用[URL encoder工具](https://www.urlencoder.org/)，將檔名貼在上面，按下ENCODE來轉換，再把下面的output複製起來。

![](/img/web-dev/fig2.png)

接著在表格中的 `download` 新增投影片的下載位置，這個路徑是 Build 過後的靜態頁面路徑，以 110 學年度為例，就是 `/slides/year2021/<URL encode後的投影片檔名>`。使用 Markdown 的連結語法:

```md
[download](/slides/year2021/<URL encode後的投影片檔名>)
```

> 注意檔案名稱大小寫：超連結路徑的大小寫要和檔案一致。
> 
> 網頁在 Linux 環境運行，Linux 檔案系統是大小寫有差的(Case sensitive)，Windows 檔案系統則是預設大小寫沒差(Non case sensitive)。所以在 Windows 開發環境會發現 URL 路徑為 `path/to/xxx.jpg` 可以讀到 `xXx.JPG`，但是放到 Linux 上面就讀不到了。

### 修改首頁資訊

回去看[檔案結構說明](#檔案結構說明)，首頁是由多個 Vue 元件組成的(在`docs/.vuepress/components/`)，並透過 `docs/.vuepress/layouts/Layout.vue` 組合這些元件。

所以修改首頁的內容只需要動到 `docs/.vuepress/components/` 的元件，這些在[檔案結構說明](#檔案結構說明)都找的到。改改文字，依樣畫葫蘆即可。

有幾個地方是每年都要改的：
- `docs/.vuepress/components/Members.vue` 更新實驗室成員
  - 把已畢業的成員移除
  - 加入新成員的照片 (`docs/.vuepress/public/images/member`底下建立該年度的資料夾，放入照片)
  - 在`<templates>`包覆的區塊，找到下面這段程式碼，在像是 `v-for="student in member109"` 和 `v-bind:src="'/images/member/109/'`的地方，把年份都加一，以下只是舉例，以實際的原始碼類推。

    ```html
    <!-- 碩二學長姐 -->
    <component v-if="Slide" :is="Slide">
        <div class="mb-page mb-p-1">
            <!-- member108 改成 member109 -->
            <div v-for="student in member108" class="member">
                <div class="mb-img">
                    <!-- /images/member/108/ 改成 /images/member/109/ -->
                    <img v-bind:src="'/images/member/108/' + student.imgFileName + '.jpg'">
                </div>
                <div class="mb-name">{{ student.name }}</div>
            </div>
        </div>
    </component>
    <!-- 碩一學弟妹 -->
    <component v-if="Slide" :is="Slide">
        <div class="mb-page mb-p-2">
            <!-- member109 改成 member110 -->
            <div v-for="student in member109" class="member">
                <div class="mb-img">
                    <!-- /images/member/109/ 改成 /images/member/110/ -->
                    <img v-bind:src="'/images/member/109/' + student.imgFileName + '.jpg'">
                </div>
                <div class="mb-name">{{ student.name }}</div>
            </div>
        </div>
    </component>
    ```
  - 在`<script>`區塊找到下面這段，一樣把 `109` 改成你要的年份。這邊的`member109`參數會被上面的`v-for="student in member109"`用到。

    ```js
    // 學長姐畢業，可以刪掉惹
    member108 : [
        {'name': '沈煥騰', 'imgFileName': '001'},
        {'name': '林哲丞', 'imgFileName': '002'},
        {'name': '凃泳村', 'imgFileName': '003'},
        {'name': '郭家瑋', 'imgFileName': '004'},
        {'name': '曾立嚴', 'imgFileName': '005'},
        {'name': '謝畯宇', 'imgFileName': '007'},
        {'name': '鄭鈞元', 'imgFileName': '006'}
    ],
    // member109 改成 member110
    member109 : [
        {'name': '許至慶', 'imgFileName': '001'},
        {'name': '紀伯燁', 'imgFileName': '002'},
        {'name': '黃禎安', 'imgFileName': '003'},
        {'name': '吳俊諺', 'imgFileName': '004'},
        {'name': '蕭禾群', 'imgFileName': '005'},
        {'name': '楊信致', 'imgFileName': '006'},
        {'name': '蕭誌成', 'imgFileName': '007'}
    ],
    // 新增 110 學弟妹
    member110 : [
        // 以此類推
    ]
    ```
    - 在`docs/.vuepress/public/images/member`下面創一個學弟妹的大頭照資料夾(例如`110`，把大頭照丟進去並重新命名。命名規則：**副檔名一律小寫jpg**，檔名則是和上面的JSON mapping的 `imgFileName` 相同，須對應到人名 `name`。
  - 視情況調整最底下的 Stylesheet，那裡是用 Stylus 語法而非 CSS (不過Stylus兼容於CSS)
- `docs/.vuepress/layouts/Layout.vue` 更新底下 Footer 的實驗室負責人聯絡資訊
  - 修改第58行即可

    ```html
    <footer>
        <div>
        國立臺北科技大學 Taipei Tech ,宏裕科技研究大樓 1423 室 Tel : (886-2) 2771-2171 #4267 實驗室負責人: 沈O騰 htshen12345 at gmail.com
        </div>
    </footer>
    ```

## Push 前的測試

如果你的環境裝好 Node、Yarn和Vuepress，在專案的根目錄執行 `yarn docs:dev` 指令，就可以先看看本機跑起來的樣子。

![](/img/web-dev/fig3.png)

## Push 之後，喝一杯咖啡的時間等待 Build & Deploy

記得設定你在Gitlab帳號的名稱和email
```sh
git configure user.email 'xxxxx@example.com'
git configure user.name 'xxxx'
```

```sh
git add .
git commit -m "Commit message 不可以亂打"
git push
```

接著到 Gitlab Pipeline 看著他跑完：https://netlab.csie.ntut.edu.tw:8878/oscar/netlab/-/pipelines

![](/img/web-dev/fig4.png)

Pipeline 分為 Build 和 Deploy 兩階段，只要Deploy那邊都是律勾勾，網站就更新完成了。

如果 Gitlab 這時跑出 503 錯誤，別慌，過一陣子回來他就會好，這是 Gitlab runner 執行時常有的毛病。

## More details if you wanna dive deep

我的部落格有紀錄亂玩 Vuepress 的過程，有興趣的話可以參考
- [VuePress 部落格架設與折騰 (一)：Themes、頁面元素、部署至 Github](https://lytzeng.github.io/posts/frontend/Vue/vuepress-blog-1.html)
- [VuePress 部落格架設與折騰 (二)：Components](https://lytzeng.github.io/posts/frontend/Vue/vuepress-blog-2.html)
- [VuePress 部落格架設與折騰 (三)：Global Computed、標籤與分類功能、Components 的應用](https://lytzeng.github.io/posts/frontend/Vue/vuepress-blog-3.html)
