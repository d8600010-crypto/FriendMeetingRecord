# 把網頁連到你的 Google 試算表

網頁是純前端的靜態檔案，沒有伺服器，所以沒辦法直接「登入」你的 Google 帳號去讀寫試算表。
最簡單、不需要使用者登入、也不用申請任何 API 金鑰的做法，是在**試算表本身**建立一個
Google Apps Script，把它部署成一個網址（Web App）。網頁只要呼叫這個網址就能讀取歌曲、
寫入點歌記錄。

你的試算表：
`https://docs.google.com/spreadsheets/d/11jJNNMv2PeYy7nEMqA2efXb5_CTYjESJWTmFLgjioSs/edit`

## 步驟 1：貼上 Apps Script 程式碼

1. 打開你的試算表。
2. 選單「擴充功能」→「Apps Script」。
3. 把裡面預設的 `Code.gs` 內容全部刪掉，貼上這個資料夾裡 **Code.gs** 的全部內容。
4. 上方工具列存檔（Ctrl/Cmd + S）。

## 步驟 2：一鍵建立資料表（第一次執行才要做）

1. 在 Apps Script 編輯器上方的函式下拉選單，選擇 **setup**。
2. 按執行（▶）。第一次會跳出授權視窗，選你的 Google 帳號 →「進階」→「前往...(不安全)」→ 允許。
   （這是 Google 對「你自己寫的腳本」的標準警告，因為程式碼是你自己貼的，可以放心允許。）
3. 執行完成後，回到試算表，你會看到多了兩個工作表：
   - **Songs**：24 位歌手、共 60+ 首歌的初始資料（就是網頁原本寫死的內容）。之後你要新增/
     刪除歌手或歌曲，直接在這張表加一列或刪一列即可，網頁會自動同步。
     欄位：`Tab | TabLabel | Category | Singer | SingerTag | Song | SongMeta`
     - `Tab` 請用四個值之一：`female-mando` / `male-mando` / `male-taiyu` / `female-taiyu`
       （對應四個分類頁籤，同一個 Tab 值的列會被分到同一個頁籤）
   - **Requests**：之後使用者在網頁上按「送出點歌」，勾選的歌曲會自動寫進這張表
     （時間戳記、分類、歌手、歌曲）。

## 步驟 3：部署成 Web App

1. 在 Apps Script 編輯器右上角「部署」→「新增部署作業」。
2. 類型選「網頁應用程式」。
3. 「執行身份」選 **我 (你的帳號)**。
4. 「誰可以存取」選 **任何人**（這樣網頁才能在不登入的狀況下呼叫它；它只會執行你寫的
   讀取/寫入邏輯，不會暴露你其他 Google 資料）。
5. 按「部署」，會再跳一次授權，一樣允許即可。
6. 部署完成後複製那個網址，長得像：
   `https://script.google.com/macros/s/AKfycb.../exec`

## 步驟 4：把網址貼進網頁

打開 `index.html`，找到最上面 `<script>` 區塊裡的這一行：

```js
const SCRIPT_URL = 'PASTE_YOUR_APPS_SCRIPT_WEB_APP_URL_HERE';
```

把 `PASTE_YOUR_APPS_SCRIPT_WEB_APP_URL_HERE` 換成你剛剛複製的網址，存檔。

完成！打開 `index.html` 就會即時從 Songs 工作表讀出歌手/歌曲清單，使用者勾選後按
「送出點歌」，就會寫進 Requests 工作表。

## 之後要更新程式碼怎麼辦？

如果之後你（或我）又改了 `Code.gs` 的內容，要回到 Apps Script 編輯器貼上新版、存檔，
再「部署」→「管理部署作業」→ 針對現有部署按「編輯」→ 版本選「新版本」→ 部署，
網址不會變，不用重新貼到網頁裡。
