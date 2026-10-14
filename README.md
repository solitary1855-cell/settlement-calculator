# 土壤沉陷量計算器

土壤沉陷量網頁計算器，可切換公噸制（t、t/m²、t/m³）或 SI 制（kN、kPa、kN/m³），電腦、平板、手機都能使用。依 2:1 應力分佈法逐層計算中點有效應力、應力增量與沉陷量。適用於淺基礎、補償式筏基，以及 Terzaghi & Peck 等值筏基（摩擦樁群）。

整個工具只有一個 `index.html`，不需要建置或安裝任何套件。

## 功能

- **輸入**：基礎寬度 B、長度 L、基礎深度 Df、荷重作用面深度 Dq、地下水位、上部結構載重、基礎自重、額外加載，以及是否扣除開挖土重。
- **土層參數**：每層可選擇三種計算方法：
  - 壓密：e0、Cc、Cr、OCR 或 σ'p
  - 彈性：限制模數 M
  - 不計
- **逐層自動計算**：中點深度、中點有效應力 σ'v0、2:1 應力增量 Δσ、Δσ/σ'v0、σ'p、沉陷量與累積沉陷。
- **結果**：影響深度以內沉陷量與總沉陷量（全部計算層）並列顯示，另列影響深度（Δσ/σ'v0 準則）、影響深度以下所佔比例，並附自動判讀提示。
- **圖表**：地層柱狀圖，與 σ'v0、Δσ、準則線的深度分佈圖。
- **輔助工具**：
  - 拖曳 ⠿ 調整土層上下順序（各層厚度不變，底深自動重算）；也可聚焦 ⠿ 後用方向鍵 ↑↓ 移動。
  - 對分按鈕：一鍵把厚黏土層拆成兩層。
  - 等值筏基深度試算：附等值基礎法示意圖（對照規範圖-解 5.5-3），可依三種情況計算 Dq：
    - (a) 均質黏土中之摩擦樁：Dq = Df + 2/3 L
    - (b) 穿過軟弱層進入承載層：Dq = 承載層頂深 + 2/3 L₂
    - (c) 端承樁：Dq = 樁尖深度
  - 一鍵複製結果表格，可直接貼到 Excel。
- **案件儲存**：輸入案件名稱後可「儲存」、「另存新案件」，從「開啟案件」切換或刪除。案件保存在使用者自己的瀏覽器中（localStorage），不會上傳到任何伺服器。
- **備份檔**：可匯出目前案件或全部案件為 `.json` 備份檔，換電腦時再匯入。
- **匯出 Word 計算書**：產生 `.docx` 計算書，內容包含設計條件、土層參數、計算方法、逐層計算結果、計算結果與判讀，以及地層柱狀與應力分佈圖。
- **單位切換**：頁面右上角可切換公噸制或 SI 制。切換時已輸入的數值會依 1 t = 9.81 kN 自動換算，畫面、圖表、複製表格與 Word 計算書的單位同步更新；單位會隨案件一起儲存。
- **其他**：支援深色模式。

> 等值基礎法示意圖係依 Terzaghi & Peck 方法重繪，未能取得規範原圖逐一核對，細部請以《建築物基礎構造設計規範》原圖為準。

## 部署到 GitHub Pages

### 方法一：用 GitHub 網頁操作（不需要安裝任何工具）

1. 登入 GitHub，按右上角 **+** → **New repository**。
2. Repository name 填 `settlement-calculator`（可自訂），選 **Public**，然後按 **Create repository**。

   > 私人（Private）repository 需要付費方案才能使用 GitHub Pages。

3. 在新 repository 頁面點 **uploading an existing file**，把 `index.html`、`README.md`、`.nojekyll` 三個檔案拖進去，按 **Commit changes**。

   > `.nojekyll` 是隱藏檔，在 macOS 上可按 `Cmd + Shift + .` 顯示。它不影響功能，漏傳也沒關係。

4. 進入 **Settings** → 左側 **Pages**。
5. 在 **Build and deployment** 的 **Source** 選 **Deploy from a branch**。**Branch** 選 `main`，資料夾選 `/ (root)`，按 **Save**。
6. 等 1～2 分鐘後重新整理 Pages 頁面，上方會顯示網址，例如：

   ```
   https://<你的帳號>.github.io/settlement-calculator/
   ```

### 方法二：用 git 指令

```bash
cd settlement-calculator
git init
git add .
git commit -m "土壤沉陷量計算器"
git branch -M main
git remote add origin https://github.com/<你的帳號>/settlement-calculator.git
git push -u origin main
```

推送完成後，依照方法一的第 4～6 步啟用 GitHub Pages。

### 更新內容

修改 `index.html` 後重新上傳（或 `git push`），GitHub Pages 會在 1～2 分鐘內自動更新。

## 本機使用

不需要部署也能用：直接用瀏覽器打開 `index.html` 即可。字型需要連網才會載入；離線時會改用系統字型，計算功能不受影響。

## 計算公式

| 項目 | 公式 |
|---|---|
| 總壓力 | q_gross = Q/(B·L) + 基礎自重 + 額外加載 |
| 淨壓力 | q_net = q_gross − σv(Df)（勾選扣除開挖土重時） |
| 有效覆土應力 | σ'v0 = σv − u。σv 由地表逐層累加，水位以上用 γt、水位以下用 γsat；u = γw·(d − GWT) |
| 應力增量（2:1） | Δσ = q_net·B·L / [(B + z)(L + z)]，z 為中點至荷重作用面深度 |
| 壓密（σ'v0+Δσ ≤ σ'p） | S = Cr·H/(1+e0)·log[(σ'v0+Δσ)/σ'v0] |
| 壓密（σ'v0+Δσ > σ'p） | S = Cr·H/(1+e0)·log(σ'p/σ'v0) + Cc·H/(1+e0)·log[(σ'v0+Δσ)/σ'p] |
| 彈性 | S = Δσ·H / M |
| 影響深度 | 荷重作用面以下第一個 Δσ/σ'v0 ≤ 準則值（軟黏土 0.10、一般 0.20）的土層中點 |

### 等值筏基的輸入方式

| 欄位 | 填入內容 |
|---|---|
| B、L | 樁群外緣尺寸 |
| Df | 樁帽底深度 |
| Dq | 依地層條件填入：<br>・均質黏土：樁帽底 + 2/3 樁長<br>・穿過軟弱層：軟弱層底 + 2/3 承載層內樁長<br>・端承樁：樁尖深度 |

開挖土重只扣 Df 以上的部分。Df～Dq 之間的土重已經包含在 σ'v0 中，**不加也不扣**。Dq 以上的土層視為不可壓縮。

## 限制

- 2:1 法為近似解，需要精確應力分佈時應改用 Boussinesq 解。
- 只計算最終主壓密與彈性沉陷，**未包含**：
  - 壓密時間效應
  - 次要壓密
  - 樁身彈性縮短
  - 鄰近基礎的應力疊加
  - 抽水降壓造成的沉陷
- σ'p < σ'v0（未完全壓密土）時，本工具按正常壓密計算。
- 本工具僅供工程初估與檢核使用。正式設計請依《建築物基礎構造設計規範》及相關規定辦理。

## 參考文獻

- Terzaghi, K. & Peck, R.B., *Soil Mechanics in Engineering Practice*, 2nd ed., Wiley (1967)
- Tomlinson, M.J. & Woodward, J., *Pile Design and Construction Practice*
- Das, B.M., *Principles of Foundation Engineering*
- EN 1997-1 Eurocode 7, 6.6.2
