# 風中三語 電子節目冊 — 部署說明

這是純靜態網站，**不需要建置、不需要 Node、不需要資料庫**。把本資料夾內所有檔案原封不動上傳到任何靜態主機即可。

## 1. 上傳

- 整個資料夾內容上傳（包含 `assets/`、`sw.js`、`manifest.webmanifest`、`LICENSES/`）。
- 可以放在網域根目錄（`https://example.tw/`）或任何子路徑（`https://example.tw/wind/`），兩種都不用改任何檔案。
- 適用主機：GitHub Pages、Cloudflare Pages、Netlify、Vercel、一般虛擬主機 public_html。
- 若同一個網域還有其他網頁，請放在子路徑或子網域，不要放根目錄：離線快取只應管到這個節目冊。

## 2. 必須是 HTTPS

離線快取（service worker／PWA）只在 HTTPS 下運作。HTTP 仍可瀏覽，但離線功能失效。
上述雲端主機預設都提供 HTTPS；自架主機請確認憑證有效。

**快取標頭**：請不要對 `index.html` 與 `sw.js` 設定長時間的 `Cache-Control: max-age`（建議 `no-cache` 或不設）。設了之後更新內容會被主機快取擋住。

## 3. 部署後自檢（5 分鐘）

1. 手機開啟網址，首屏看到「風中三語」金色標題、下方緊接「演出曲目」。
2. 點任一曲目可展開樂章與解說；點演出者「閱讀全文」可展開。
3. 底部「曲目｜介紹｜演出者」三個按鈕可跳轉。
4. **離線測試**：頁面完整載入後開飛航模式，重新整理，內容與照片仍顯示。
5. Chrome 桌機：F12 → Application → Service Workers 看到 `sw.js` 為 activated；Manifest 無錯誤。
6. 以子路徑部署時，確認瀏覽器 Network 面板沒有 404（特別是 `assets/fonts/*.woff2`）。

## 4. 部署完成後請回傳最終網址

正式 QR Code（給現場立牌與宣傳物）必須用最終網址產生，請把網址回傳給製作方。

**網址必須以 `/` 結尾**（例如 `https://example.tw/wind/`，不是 `https://example.tw/wind`）。少了斜線，離線快取的範圍對不上，觀眾在收訊差的地方會打不開。

網址確定後，製作方會同時把 `index.html` 內的 `og:image` 改成絕對網址（社群分享預覽圖需要）。

## 5. 日後改內容

- 文字直接改 `index.html`。**若新增了頁面上原本沒有的中文字**，需重新產生字體子集（製作方執行 `tools/subset_fonts.py`），否則該字會以系統字體顯示。
- 任何檔案改動後需重新產生 `sw.js`（製作方執行 `tools/gen_sw.py`）。若來不及找製作方，**手動把 `sw.js` 第 2 行的 `CACHE = 'wind-v........'` 改成任何新字串**（例如加上日期），舊訪客下次開啟就會拿到新版。頁面本身（`index.html`）的更新不需要改 `sw.js` 也會在下一次開啟時生效，但圖片、字體等資源的更新需要。

## 6. 演出後下架

演出者照片的授權以本次演出為限。演出結束後若要撤下：
1. 刪除 `assets/img/performer_*.webp`，並從 `index.html` 移除三個 `<details class="bio">` 區塊（或整個網站下架）。
2. 依第 5 節改 `sw.js` 的 `CACHE` 字串，讓曾經開過的觀眾手機在下次連線時清掉舊快取。
3. 已離線快取在觀眾裝置上的副本無法遠端刪除；若對方從此不再開啟該網址，副本會隨瀏覽器清理週期消失。

## 7. 授權

字體 Noto Serif TC、EB Garamond 為 SIL Open Font License 1.1，授權全文在 `LICENSES/`。演出者照片與 logo 僅供本演出宣傳使用。
