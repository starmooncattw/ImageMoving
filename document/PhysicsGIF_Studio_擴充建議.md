# PhysicsGIF_Studio.html 擴充建議

參考 AnimationHTML_Generator.html 的功能比較後，以下為**確認無重疊**的建議擴充項目。

---

## 🎯 優先度排序

### ⭐⭐⭐ 高優先（建議優先實作）

#### 1. 移動模式擴充 ⚠️ parrot 部分重疊
- **圓周運動（可調半徑）** - ⚠️ 已有 parrot (固定半徑40)，建議擴充為可調
- **來回循環** - ❌ 無，水平/垂直來回
- **之字形** - ❌ 無，Z字形路徑
- **隨機彈跳** - ❌ 無，碰壁反彈

#### 2. 陰影/發光效果 ❌ 無
- 投影模式 - 可調顏色、強度
- 發光模式 - 外發光效果
- ⚠️ 螢火蟲粒子有用 shadowBlur，但無通用控制

### ⭐⭐ 中優先

#### 3. ~~偽3D效果（翻書效果）~~ ✅ 已有
- ~~Y軸翻轉模擬 - 水平壓縮/拉伸~~
- ~~X軸翻轉模擬 - 垂直壓縮/拉伸~~
- ✅ 已實作：flipH/flipV 使用 scale 模擬連續翻轉

#### 4. 透明度控制 ❌ 無
- 整體透明度 0-100%
- 簡單但實用

### ⭐ 低優先

#### 5. 扭曲效果 ❌ 無
- 水平/垂直拉伸 (skew)
- 可調強度 1-30度

#### 6. 抖動效果 ❌ 無
- 隨機位移抖動
- 可調強度 1-10px

---

## 📋 功能對照表

| 功能類別 | PhysicsGIF_Studio | AnimationHTML_Generator | 建議 |
|---------|-------------------|-------------------------|------|
| 物理模式 | ✅ 13種 | ❌ | 保持優勢 |
| 移動路徑 | 5種 | 14種 | +圓周/來回/之字 |
| 翻轉動畫 | ✅ 連續翻轉 | 靜態翻轉 | Physics優勢 |
| 旋轉 | 平面2D | 平面+偽3D | 已有flipH/V |
| 視覺濾鏡 | 7種 | 6種 | +陰影/發光 |
| 粒子系統 | ✅ 8種 | ❌ | 保持優勢 |
| 透明度 | ❌ | ✅ | +全域透明度 |
| 拖曳定位 | ✅ | ❌ | 已完成 |

---

## 💡 實作建議

### 移動模式新增項目
```javascript
moveMode 選項擴充：
- parrot → circleCustom: 可調半徑的圓周運動 (目前 parrot 固定半徑40)
- horizontalLoop: 水平來回
- verticalLoop: 垂直來回
- zigzag: 之字形
- randomBounce: 隨機彈跳

新增參數：
- circleRadius: 50-400 (圓周半徑，預設 40)
- loopDistance: 100-800 (來回距離)
- zigzagAmplitude: 20-200 (之字形幅度)

目前已有（line 494）：
if (params.moveMode === 'parrot') {
    motionX = Math.cos(cycle)*40;  // 固定半徑
    motionY = Math.sin(cycle)*40;
}
```

### 陰影/發光
```javascript
新增參數：
- shadowType: 'none'|'drop'|'glow'
- shadowColor: '#000000'
- shadowBlur: 0-20
```

### ~~偽3D翻轉~~ ✅ 已實作
```javascript
✅ 已有功能（line 499-500）：
- flipH: 左右連續翻轉 → motionScaleX = Math.sin(cycle)
- flipV: 上下連續翻轉 → motionScaleY = Math.sin(cycle)

背面處理（line 512-517）：
- isBackFace = totalScaleX < 0
- 支援鏡像/變暗/純色三種背面模式

✅ 無需新增，功能已完整
```

### 透明度
```javascript
新增參數：
- opacity: 0-1 (全域透明度)

應用在最外層 ctx.globalAlpha
```

---

## ⚠️ 注意事項與技術限制

### 1. Canvas 2D 環境限制
- ✅ **可用API**：translate、rotate、scale、transform、globalAlpha、shadowBlur
- ❌ **不可用**：CSS 3D transform (perspective, rotateX, rotateY)
- ⚠️ **替代方案**：使用 scale 模擬 3D 翻轉效果

### 2. GIF 錄製特性
- **逐幀錄製**：每幀獨立繪製到 Canvas，再編碼為 GIF
- **效能考量**：複雜效果會增加錄製時間
- **建議**：避免每幀大量運算（如多次模糊疊加）

### 3. 定位差異
- AnimationHTML_Generator.html → HTML動畫檔（使用 CSS/WebGL，OBS捕捉）
- PhysicsGIF_Studio.html → GIF圖檔（使用 Canvas 2D，即時錄製）

### 4. 保持特色
- 物理模式是 PhysicsGIF_Studio 的獨特優勢
- 不需完全複製 AnimationHTML_Generator 的所有功能

---

## ✅ 各功能 GIF 匯出可行性

| 功能 | 可行性 | 說明 |
|------|--------|------|
| 圓周/來回/之字移動 | ✅ 完全可行 | 數學運算，無問題 |
| 陰影效果 | ✅ 完全可行 | ctx.shadowBlur 原生支援 |
| 發光效果 | ⚠️ 可行但慢 | 需多次繪製模擬 |
| 偽3D翻轉 | ✅ 可行 | scale 模擬，非真實3D |
| 扭曲效果 | ✅ 完全可行 | ctx.transform 支援 |
| 抖動效果 | ✅ 完全可行 | translate 隨機偏移 |
| 透明度控制 | ✅ 完全可行 | ctx.globalAlpha |

---

## 📌 已有/已完成項目

### 原本就有的功能
- ✅ 左右/上下連續翻轉動畫（flipH/flipV, line 499-500）
- ✅ 背面處理機制（鏡像/變暗/純色, line 512-517）
- ✅ 13種物理模式（蹦蹦跳、軟Q彈跳等）
- ✅ 8種粒子系統（下雪、愛心、星星等）
- ✅ 模糊效果（blur, line 141, 553）
- ⚠️ 圓周運動（parrot, line 494，但半徑固定40）

### 近期完成修正
- ✅ 拖曳調整圖片位置
- ✅ 速度小數點控制
- ✅ 波浪效果修正
- ✅ 粒子系統速度調整
- ✅ 倒影效果修正
- ✅ 預覽/錄製速度同步
