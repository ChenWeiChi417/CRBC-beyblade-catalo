# CRBC 戰鬥陀螺 X (BX / UX / CX) 商品圖鑑網站 - AI 開發規範 (AGENTS.md)

## 1. 專案目的
本專案為「CRBC」戰鬥陀螺團隊所建立的 Beyblade X 官方發售商品視覺化圖鑑網站，完整涵蓋 **BX（Basic Line）**、**UX（Unique Line）** 與 **CX（Custom Line）** 三大系列。目標是透過高度模組化的網格卡片排版與滾動視差動畫，讓團隊成員與玩家能快速檢索歷代陀螺商品編號與中文譯名，並提供 AI 一套標準化的擴充規範，以便每月自動更新最新發售的陀螺資訊。

## 2. 跨系列視覺風格與差異化設定
三大系列共享相同的骨架，但透過外部 CSS 與右側裝飾條紋區分系列代表色。AI 在建立或修改特定系列頁面時，必須嚴格對應以下設定：

| 系列名稱 | 頁面標題 (`<title>`) | 綁定之 CSS 檔案 | 標題卡文字 (`.series-label-text`) | 右側條紋樣式 (`.right-decoration`) |
| :--- | :--- | :--- | :--- | :--- |
| **BX 系列** | `BX系列商品一覽 - Beyblade X` | `../static/style5.bx.css` | `BX<br>SERIES` | `r-blue` / `r-black` / `r-blue` |
| **UX 系列** | `UX系列商品一覽 - Beyblade X` | `../static/style6.ux.css` | `UX<br>SERIES` | `r-orange` / `r-black` / `r-orange` |
| **CX 系列** | `CX系列商品一覽 - Beyblade X` | `../static/style4.cx.css` | `CX<br>SERIES` | `r-pink` / `r-black` / `r-pink` |

## 3. 頁面標準 DOM 結構
每一個系列頁面皆由以下 5 個核心區塊依序組成，AI 擴充時不得任意調換順序或刪除區塊：
1. **導航與 Header**：包含回首頁按鈕 (`.home-btn`，圖片為 `HOME.png`) 與頂部置中 Logo (`logo.png`)。
2. **中間主要內容區 (`.content-area` > `.product-grid`)**：
   * **第 1 格**：固定為該系列的標題格 (`.series-label-card`)。
   * **中間格**：依編號由小到大排列的商品卡片 (`.product-card.scroll-fade`)。
   * **最後 1 格**：固定為「敬請期待」預留卡片（CX 系列需額外加上 `coming-soon` class）。
3. **前景滾動人物 (`.char-scroll`)**：固定依序放置 `char-top` (`x.png`)、`char-mid` (`Bird.png`)、`char-bot` (`Multi.png`)。
4. **背景裝飾**：包含 `.left-sidebar` 與對應系列顏色的 `.right-decoration`。
5. **滾動偵測腳本 (`<script>`)**：使用 `IntersectionObserver` (設定 `threshold: 0.01`, `rootMargin: "0px 50% 10% 0px"`) 監控 `.char-scroll` 與 `.scroll-fade`，進入視窗時加上 `.active` 觸發進場動畫並解除觀察 (`unobserve`)。

## 4. 新增商品卡片之標準格式 (HTML Template)
當使用者要求在任一系列新增商品時，AI 必須嚴格遵守以下 HTML 結構（以新增 `BX-12` 為例）：

```html
            <div class="product-card scroll-fade">
                <img src="../static/{系列小寫}/{系列小寫}{兩位數編號}.png" alt="{系列大寫}-{兩位數編號}" class="prod-img">
                <div class="prod-title"><span>{系列大寫}-{兩位數編號} {商品中文名稱}</span></div>
            </div>
