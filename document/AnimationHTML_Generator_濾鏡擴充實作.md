# AnimationHTML_Generator 濾鏡擴充實作

## 架構說明

| 項目 | PhysicsGIF_Studio | AnimationHTML_Generator |
|---|---|---|
| 渲染方式 | Canvas 逐幀像素操作 | CSS 動畫 + 瀏覽器渲染 |
| 輸出格式 | GIF | HTML（OBS Browser Source） |
| 濾鏡機制 | `ctx.filter` / drawImage 操作 | CSS `filter` / `@keyframes` / SVG filter |

兩者渲染管線不同，**無法共用濾鏡程式碼**，需各自實作。

---

## 實作清單

### ✅ 已完成

#### 1. 負片 (Invert)
- 靜態模式：`filter: invert(100%)`
- 閃爍模式：`@keyframes invert-flash` 1秒切換
- 預覽與輸出 HTML 皆正常

---

### 📋 待實作

#### 1. 負片 (Invert)
- **難度**：低
- **方案**：CSS `filter: invert(100%)` + 閃爍動畫
- **預覽**：直接套用 CSS filter 到 `#previewImg`
- **輸出**：`@keyframes` 閃爍 或 靜態 `filter: invert(100%)`
- **PhysicsGIF 對應**：`invert: true`，speed > 0 時每 5 frame 閃爍一次

```css
/* 靜態 */
filter: invert(100%);

/* 閃爍版 */
@keyframes invert-flash {
    0%, 49% { filter: invert(100%); }
    50%, 100% { filter: none; }
}
```

- [x] UI 控制項（checkbox + 模式選單）
- [x] 預覽套用
- [x] 輸出 HTML 嵌入

---

#### 2. 老電影 (Sepia)
- **難度**：低
- **方案**：CSS `filter: sepia(100%)`
- **預覽**：直接套用 CSS filter
- **輸出**：靜態 CSS filter
- **PhysicsGIF 對應**：`sepia: true`

```css
filter: sepia(100%);
```

- [ ] UI 控制項（checkbox）
- [ ] 預覽套用
- [ ] 輸出 HTML 嵌入

---

#### 3. 故障 (Glitch)
- **難度**：中
- **方案**：CSS `@keyframes` 使用 `clip-path` 切片 + `translate` 偏移模擬
- **預覽**：動態插入 `<style>` keyframes
- **輸出**：嵌入 `@keyframes` 動畫
- **PhysicsGIF 對應**：Canvas 兩次偏移繪圖 + `lighten` 合成（**無法移植**，重新實作）
- **注意**：效果與 PhysicsGIF 不完全相同，為 CSS 模擬版本

```css
@keyframes glitch {
    0%   { clip-path: inset(20% 0 60% 0); transform: translate(-5px); }
    20%  { clip-path: inset(50% 0 20% 0); transform: translate(5px); }
    40%  { clip-path: inset(10% 0 70% 0); transform: translate(-3px); }
    60%  { clip-path: inset(70% 0 10% 0); transform: translate(3px); }
    80%  { clip-path: inset(40% 0 40% 0); transform: translate(-5px); }
    100% { clip-path: inset(0); transform: translate(0); }
}
```

- [ ] UI 控制項（checkbox + 強度 range）
- [ ] 預覽套用
- [ ] 輸出 HTML 嵌入
- [ ] OBS 實機測試

---

#### 4. 馬賽克 (Pixel)
- **難度**：中（效果有限）
- **方案**：`image-rendering: pixelated` + CSS `scale` 縮小再放大
- **預覽**：CSS 套用
- **輸出**：CSS 內嵌
- **PhysicsGIF 對應**：`imageSmoothingEnabled = false` Canvas 縮放（**無法移植**，效果較 Canvas 差）
- **注意**：純 CSS 馬賽克效果有限，像素區塊大小控制不精確

```css
image-rendering: pixelated;
transform: scale(0.1) scale(10); /* 實際需外層 wrapper 配合 */
```

- [ ] UI 控制項（checkbox + 像素大小 range）
- [ ] 研究可行的 CSS 實作方案
- [ ] 預覽套用
- [ ] 輸出 HTML 嵌入
- [ ] 效果確認後決定是否正式加入

---

#### 5. 波浪 (Wave)
- **難度**：高
- **方案**：SVG `<feTurbulence>` + `<feDisplacementMap>` filter
- **預覽**：SVG filter 套用
- **輸出**：SVG filter 嵌入 HTML
- **PhysicsGIF 對應**：Canvas 逐列切片水平偏移（**無法移植**，完全不同機制）
- **風險**：SVG filter 動態動畫在 OBS Browser Source 相容性待確認

```html
<svg style="display:none">
  <filter id="waveFilter">
    <feTurbulence type="sine" baseFrequency="0.02 0" numOctaves="1" result="wave"/>
    <feDisplacementMap in="SourceGraphic" in2="wave" scale="20" xChannelSelector="R"/>
  </filter>
</svg>
```

```css
filter: url(#waveFilter);
```

- [ ] 研究 SVG filter 動態動畫方案
- [ ] OBS 相容性測試（**最優先確認，再決定是否實作**）
- [ ] UI 控制項
- [ ] 預覽套用
- [ ] 輸出 HTML 嵌入

---

## 實作順序建議

1. **負片** → 最簡單，先完成熱身
2. **老電影** → 同樣簡單，一併完成
3. **故障** → 中等複雜，完成後需 OBS 測試
4. **馬賽克** → 確認 CSS 效果是否可接受，再決定做法
5. **波浪** → 最後，需先確認 OBS 相容性

---

## 測試記錄

| 濾鏡 | 預覽正常 | OBS 實測 | 備註 |
|---|---|---|---|
| 負片 | ✅ | 待測 | |
| 老電影 | - | - | |
| 故障 | - | - | |
| 馬賽克 | - | - | |
| 波浪 | - | - | |
