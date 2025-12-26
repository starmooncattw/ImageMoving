# AnimationHTML_Generator.html 擴充建議

參考 PhysicsGIF_Studio.html 的功能與設計，以下為建議擴充項目。

---

## 🎯 優先度排序

### ⭐⭐⭐ 高優先（建議優先實作）

#### 1. 即時預覽功能 ✅ 已完成 (2025-12-26)
**現況**: ~~需產生檔案後才能預覽~~ → **已實作左右分欄 + 即時預覽**
**PhysicsGIF**: 即時 Canvas 預覽，調整參數立即顯示

**已實作** - 使用動態 DOM 預覽（保持 CSS 動畫輸出一致性）:
- ✅ 改為左右分欄佈局（左側控制、右側預覽）
- ✅ 右側使用 `<img>` + 動態產生 CSS 動畫
- ✅ 參數變更時即時更新預覽區的 CSS
- ✅ 所有14種移動模式完整預覽（左右、上下、對角線、圓周、之字形、彈跳等）
- ✅ 所有9種視覺特效完整預覽（旋轉、縮放、扭曲、傾斜、模糊、陰影、色相、3D、抖動）
- ✅ 實作 transform 效果互斥機制（避免 CSS 衝突）
- ✅ 預覽區響應式調整（跟隨瀏覽器視窗大小）
- ✅ 圖片大小不受限制（移除30%上限）
- **❌ 不實作拖曳功能**（實際效用低，使用者可在 OBS 中直接調整位置）

**技術方案**:
```html
<!-- 左右分欄佈局 -->
<div style="display: flex; height: 100vh;">
    <div class="sidebar" style="width: 480px; overflow-y: auto;">
        <!-- 設定表單 -->
    </div>
    <div class="preview-area" style="flex: 1; background: #252525;">
        <div id="animationContainer" style="position: relative; width: 100%; height: 100%;">
            <img id="previewImg" style="position: absolute;">
        </div>
    </div>
</div>
```

```javascript
// 參數變更時動態更新預覽
function updatePreview() {
    const img = document.getElementById('previewImg');
    const styleSheet = document.getElementById('dynamicStyle');

    // 根據設定動態產生 CSS 動畫
    const css = generateAnimationCSS(currentParams);
    styleSheet.textContent = css;

    // 套用到預覽圖片
    img.style.animation = generateAnimationString(currentParams);
}
```

**為什麼不實作拖曳定位**:
1. **OBS 本身就能調整位置** - 使用者在 OBS 中拖曳「瀏覽器來源」更直覺
2. **實作複雜度高** - 需處理拖曳與移動模式的衝突
3. **實際效用低** - AnimationHTML 主要用於動態移動，靜態定位需求少
4. **與輸出不一致** - 預覽中的位置與 OBS 實際顯示可能不同（螢幕尺寸差異）

---

#### 2. 粒子系統 ❌ 無
**PhysicsGIF**: 8種粒子（下雪、愛心、櫻花、螢火蟲、泡泡、星星、閃光、彩紙）

**建議**:
- 新增「粒子特效」選單
- 使用 Canvas 繪製粒子
- 產生 HTML 時嵌入粒子動畫程式碼

**注意**: 會增加 HTML 檔案大小（需模組化程式碼）

---

### ⭐⭐ 中優先

#### 4. 物理變形效果 ❌ 無
**PhysicsGIF**: 13種物理模式（蹦蹦跳、軟Q彈跳、果凍等）

**建議 - CSS 模擬**:
```css
@keyframes bounce {
    0% { transform: scaleY(1) scaleX(1); }
    50% { transform: scaleY(0.8) scaleX(1.2); }  /* 落地擠壓 */
    100% { transform: scaleY(1) scaleX(1); }
}
```

可新增:
- 彈跳擠壓（蹦蹦跳）
- 果凍晃動（poyunpoyun）
- 飄浮（上下緩動）

---

#### 5. 濾鏡效果擴充 ⚠️ 部分
**PhysicsGIF 有但 AnimationHTML 無**:
- 波浪扭曲
- 故障特效
- 老電影
- 負片閃爍
- 馬賽克

**建議實作**:
```css
/* 波浪 - 使用 SVG filter */
filter: url(#waveFilter);

/* 故障 */
@keyframes glitch {
    0% { transform: translate(0); }
    20% { transform: translate(-5px, 5px); }
    40% { transform: translate(5px, -5px); }
}

/* 老電影 */
filter: sepia(100%) contrast(1.2);
/* + 雜訊紋理 */

/* 馬賽克 */
image-rendering: pixelated;
/* + scale 縮放 */
```

---

#### 6. 倒影/鏡像效果 ❌ 無
**PhysicsGIF**: 鏡像（雙人對稱）、倒影（水面反射）

**建議**:
```css
/* 鏡像 */
.mirror::after {
    content: '';
    position: absolute;
    transform: scaleX(-1);
}

/* 倒影 */
.reflection::after {
    transform: scaleY(-1);
    opacity: 0.4;
    filter: blur(2px);
}
```

---

### ⭐ 低優先

#### 7. 快速範本視覺回饋 ⚠️ 部分
**PhysicsGIF**: 範本按鈕有 active 狀態
**AnimationHTML**: 範本只套用設定，無回饋

**建議**:
- 點擊範本後按鈕顯示 active 樣式
- 配合即時預覽立即顯示效果

---

#### 8. 背景模式選擇 ❌ 無
**PhysicsGIF**: 透明/去背優化/純色/綠幕
**AnimationHTML**: 固定透明

**建議**: 新增背景顏色選項（產生 HTML 時套用）

---

## 📊 功能對照表

| 功能 | AnimationHTML | PhysicsGIF | 優先度 | 說明 |
|------|--------------|-----------|--------|------|
| **即時預覽** | ✅ 已完成 | ✅ | ⭐⭐⭐ | 大幅提升體驗 |
| **拖曳定位** | ❌ | ✅ | ❌ 不實作 | OBS 可調整，效用低 |
| **粒子系統** | ❌ | ✅ 8種 | ⭐⭐⭐ | 增加視覺豐富度 |
| **物理變形** | ❌ | ✅ 13種 | ⭐⭐ | CSS 可模擬部分 |
| **濾鏡效果** | ⚠️ 6種 | ✅ 7種 | ⭐⭐ | 補充波浪/故障 |
| **倒影鏡像** | ❌ | ✅ | ⭐⭐ | CSS 易實作 |
| **移動路徑** | ✅ 14種 | ✅ 5種 | - | 保持優勢 |
| **陰影發光** | ✅ | ❌ | - | 保持優勢 |
| **3D旋轉** | ✅ | ❌ | - | 保持優勢 |
| **匯入匯出** | ✅ | ❌ | - | 保持優勢 |

---

## 💡 佈局改版建議

### 目標: 改為左右分欄（與 PhysicsGIF 一致）

```
┌─────────────────────────────────────┐
│  Animation HTML Generator           │
├──────────┬──────────────────────────┤
│          │                          │
│  控制面板 │     即時預覽 Canvas      │
│  480px   │     flex: 1             │
│          │                          │
│  [範本]  │    ┌──────────────┐     │
│  [移動]  │    │              │     │
│  [特效]  │    │   動畫預覽    │     │
│  [設定]  │    │              │     │
│          │    └──────────────┘     │
│  [產生]  │                          │
└──────────┴──────────────────────────┘
```

### CSS 調整

```css
body {
    display: flex;
    overflow: hidden;
    height: 100vh;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.sidebar {
    width: 480px;
    background: #1e1e1e;
    border-right: 1px solid #333;
    overflow-y: auto;
    padding: 15px;
    scrollbar-width: thin;
}

.main-area {
    flex: 1;
    background-color: #252525;
    /* 棋盤背景 */
    background-image:
        linear-gradient(45deg, #2a2a2a 25%, transparent 25%),
        linear-gradient(-45deg, #2a2a2a 25%, transparent 25%),
        linear-gradient(45deg, transparent 75%, #2a2a2a 75%),
        linear-gradient(-45deg, transparent 75%, #2a2a2a 75%);
    background-size: 20px 20px;
    display: flex;
    align-items: center;
    justify-content: center;
}

canvas {
    max-width: 95%;
    max-height: 95vh;
    box-shadow: 0 0 30px rgba(0,0,0,0.5);
}
```

---

## 🔧 即時預覽實作邏輯

### 方案: 動態 DOM + CSS 動畫（與輸出一致）

```javascript
// 全域狀態
let previewImg = null;
let dynamicStyleSheet = null;

// 初始化預覽
function initPreview() {
    previewImg = document.getElementById('previewImg');
    dynamicStyleSheet = document.createElement('style');
    dynamicStyleSheet.id = 'dynamicStyle';
    document.head.appendChild(dynamicStyleSheet);

    // 監聽所有參數變更
    document.querySelectorAll('input, select').forEach(el => {
        el.addEventListener('change', updatePreview);
        el.addEventListener('input', updatePreview);
    });
}

// 更新預覽
function updatePreview() {
    const config = getCurrentConfig(); // 取得目前所有設定

    // 設定圖片
    previewImg.src = config.image;
    previewImg.style.width = config.size + '%';
    previewImg.style.opacity = config.opacity / 100;

    // 產生 CSS 動畫（複用現有的 generateHTMLTemplate 邏輯）
    const css = generateAnimationCSS(config);
    dynamicStyleSheet.textContent = css;

    // 套用動畫
    const animations = generateAnimationList(config);
    previewImg.style.animation = animations.join(', ');

    // 套用濾鏡
    const filters = generateFilterList(config);
    previewImg.style.filter = filters.join(' ');

    // 套用翻轉
    applyFlipTransform(config.flip);
}

// 產生動畫 CSS（從現有邏輯提取）
function generateAnimationCSS(config) {
    let css = '';
    // ... 根據 config 產生 @keyframes
    // 複用 getAnimationScript() 中的邏輯
    return css;
}
```

**優點**:
- ✅ 與最終輸出完全一致（都是 CSS 動畫）
- ✅ 不需要重新實作動畫邏輯（複用現有程式碼）
- ✅ 效能好（瀏覽器原生 CSS 動畫）
- ✅ 即時更新（參數變更立即顯示）

---

## ⚠️ 技術限制與注意事項

### 1. 預覽與輸出的差異
- ⚠️ 預覽區尺寸與 OBS 實際尺寸可能不同
- ⚠️ 部分複雜動畫（randomBounce、circle 隨機中心）需重新載入才能完全重置
- ✅ 使用相同的 CSS 動畫邏輯，確保一致性

### 2. 粒子系統的檔案大小
- 粒子程式碼約 2-3KB（未壓縮）
- 建議將 8 種粒子程式碼模組化
- 只嵌入使用者選擇的粒子類型

### 3. 濾鏡的瀏覽器相容性
- 波浪扭曲需要 SVG filter
- 確保 OBS 瀏覽器來源支援
- 故障效果可用純 CSS transform 替代

### 4. 為什麼不使用 Canvas 預覽
- ❌ 需要將 CSS 動畫邏輯轉換為 Canvas 繪製（重複實作）
- ❌ Canvas 與最終 CSS 輸出不一致，可能產生差異
- ✅ 使用動態 DOM + CSS 可直接複用現有程式碼
- ✅ 預覽效果與最終輸出 100% 一致

---

## ✅ 實作步驟建議

### 階段 1: 佈局改版（高優先）✅ 已完成 (2025-12-26)
1. ✅ 修改 CSS 為左右分欄（flex 佈局）
2. ✅ 將控制項移至左側 sidebar（480px 寬，可滾動）
3. ✅ 右側放置預覽區（棋盤背景 + 動畫容器）

### 階段 2: 即時預覽（高優先）✅ 已完成 (2025-12-26)
1. ✅ 新增預覽用 `<img>` 元素和動態 `<style>` 標籤
2. ✅ 提取 CSS 產生邏輯為獨立函式
3. ✅ 監聽所有 input/select 變更事件，呼叫 `updatePreview()`
4. ✅ 實作所有14種移動模式與9種視覺特效預覽
5. ✅ 實作 transform 效果互斥邏輯（避免 CSS 衝突）

### 階段 3: 粒子系統（高優先）
1. 新增粒子選項到「視覺特效」區塊（8種粒子）
2. 實作粒子 CSS/JS 程式碼產生邏輯（參考 PhysicsGIF）
3. 在產生 HTML 時嵌入選定的粒子程式碼
4. 預覽區也要顯示粒子效果

### 階段 4: 濾鏡擴充（中優先）
1. 波浪效果（SVG filter 或 CSS skew 模擬）
2. 故障特效（CSS transform + 動畫）
3. 馬賽克效果（image-rendering: pixelated）

### 階段 5: 其他特效（低優先）
1. 倒影效果（::after 偽元素 + scaleY(-1)）
2. 鏡像效果（::after 偽元素 + scaleX(-1)）
3. 物理變形效果（CSS 動畫模擬彈跳、果凍等）

---

## 📌 保持現有優勢

**AnimationHTML 獨有功能**:
- ✅ 14種移動路徑（vs PhysicsGIF 5種）
- ✅ 陰影/發光特效
- ✅ 3D旋轉（rotateX/Y/Z）
- ✅ 匯入/匯出設定（Base64）
- ✅ 抖動效果
- ✅ 扭曲/傾斜搖晃

**建議**: 保持這些優勢，不需改動

---

**製作**: 依據 PhysicsGIF_Studio.html 功能分析
**目的**: 提升 AnimationHTML_Generator 使用體驗與功能豐富度
**原則**: 參考優點、保持特色、避免衝突
