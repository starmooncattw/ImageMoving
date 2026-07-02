# Image Moving Tools — 開發規劃

## 現有工具

| 檔案 | 功能 |
|------|------|
| `AnimationHTML_Generator.html` | 上傳圖片，設定移動/旋轉/縮放/濾鏡/粒子，輸出獨立 HTML 動畫檔 |
| `PhysicsGIF_Studio.html` | 上傳圖片，套用物理動態效果，輸出 GIF |

---

## 待開發工具

### 1. GIF Editor（GIF 幀編輯器）✅ 完成 — `GIF_Editor.html`

**核心功能**
- 上傳 GIF → 解析所有幀，縮圖列表顯示
- 刪除、複製、拖曳重排幀
- 調整全部或個別幀的延遲時間（播放速度）
- 裁切、縮放尺寸
- 加文字（字型、顏色、位置）
- 調整亮度、對比、色相等濾鏡
- 輸出成新 GIF

**技術需求**
- `gifuct-js`（解碼 GIF 拆幀）→ 需內嵌至 HTML
- `gif.js`（重新編碼輸出）→ 已有範本可參考

---

### 2. GIF Combiner（GIF 合成器）

**上傳支援**
- GIF（動態圖層，逐幀播放）
- PNG / JPG / WebP（靜態圖層，每幀顯示同一張）

**合併模式**

| 模式 | 說明 |
|------|------|
| 疊加（圖層） | 多圖層上下疊放，可設定位置、大小、不透明度 |
| 序列 | A 播完接 B，多個 GIF 串接 |
| 並排 | 左右或上下同步播放 |

**疊加模式細節**
- 每個圖層可設定 X/Y 位置、縮放、不透明度
- 圖層順序可拖曳調整
- 輸出幀數以最長的 GIF 為基準
- 靜態圖片自動複製到每幀

**常見應用場景**
- GIF 加浮水印 / Logo
- GIF 加背景圖
- 多個 GIF 合併成一個
- 靜態圖片串成 GIF

**技術需求**
- `gifuct-js`（解碼 GIF 拆幀）→ 與 GIF Editor 共同需求
- `gif.js`（重新編碼輸出）→ 已有範本可參考

---

## 技術共用說明

兩個新工具都需要相同的核心函式庫：

- **gifuct-js** — 解碼 GIF、拆出每幀 ImageData
- **gif.js + gif.worker.js** — 重新編碼輸出 GIF（參考 PhysicsGIF_Studio 的內嵌方式）

建議先完成 **GIF Editor**，再開發 **GIF Combiner**（兩者解碼邏輯相同，可複用）。

---

## 部署方式

- 純靜態 HTML，所有處理在瀏覽器本機完成，圖片不上傳伺服器
- 托管於 GitHub Pages：`https://starmooncattw.github.io/ImageMoving/`
- `index.html` 作為工具集入口頁
