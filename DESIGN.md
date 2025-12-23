# 圖片動畫工具 - 設計文件

## 專案目標
單機 HTML 工具，可設定圖片動畫效果並產生透明背景結果頁面。

## 技術架構
- **config.html**: 設定介面
- **result.html**: 結果展示頁
- **IndexedDB**: 資料持久化 (支援大檔案)
- **純前端**: 無需網路、伺服器

## 功能規格

### 基礎設定
- 圖片上傳: JPG/PNG/GIF/WebP
- 檔案限制: 建議 < 50MB (超大檔案可能影響效能)
- 即時預覽

### 移動模式
1. 靜止
2. 隨機出現
3. 隨機移動(彈跳)
4. 水平: 左→右 / 右→左 / 來回
5. 垂直: 上→下 / 下→上 / 來回
6. 對角線 (4方向)
7. 圓周運動
8. 之字形

### 移動參數
- **速度**: 1-10 (適用所有模式)
  - 隨機出現: 控制出現間隔
  - 其他: 控制移動速度
- **停留時間**: 0-5秒
  - 隨機出現: 每次停留時長
  - 來回模式: 兩端停留
- **淡入淡出**: 開/關 (適用所有模式)
- **範圍**: 全螢幕

### 圖片變換
- 翻轉: 無/水平/垂直/兩者

### 視覺特效 (可疊加)

#### 1. 旋轉
- 模式: 順時針/逆時針/來回擺動
- 速度: 1-10

#### 2. 縮放脈動
- 模式: 呼吸效果/放大縮小
- 幅度: 0.5-2倍

#### 3. 扭曲
- 方向: 水平/垂直拉伸
- 強度: 1-30度

#### 4. 傾斜搖晃
- 模式: 左右/上下搖晃
- 角度: 1-45度

#### 5. 模糊
- 類型: 動態/高斯模糊
- 強度: 0-10px

#### 6. 陰影/發光
- 類型: 投影/發光
- 顏色: 色碼選擇器
- 強度: 0-20px

#### 7. 色相旋轉
- 速度: 1-10 (彩虹循環)

#### 8. 3D旋轉
- 軸向: X/Y/Z軸
- 速度: 1-10

#### 9. 抖動
- 強度: 1-10px

### 其他設定
- 圖片大小: 10-200%
- 透明度: 0-100%

### 附加功能
- 預覽效果
- 儲存並開啟結果頁
- 重置設定
- 匯出/匯入設定 (JSON)
- 預設範本

## 資料結構

```javascript
{
  image: "data:image/gif;base64...",
  movement: {
    mode: "leftToRight",
    speed: 5,
    stayDuration: 2,
    fade: true
  },
  flip: "horizontal",
  effects: {
    rotate: { enabled: true, mode: "clockwise", speed: 5 },
    scale: { enabled: true, mode: "pulse", range: 1.5 },
    skew: { enabled: false, direction: "horizontal", strength: 15 },
    tilt: { enabled: false, mode: "swing", angle: 30 },
    blur: { enabled: false, type: "motion", strength: 5 },
    shadow: { enabled: false, type: "drop", color: "#000000", strength: 10 },
    hueRotate: { enabled: false, speed: 5 },
    rotate3d: { enabled: false, axis: "y", speed: 5 },
    shake: { enabled: false, strength: 5 }
  },
  size: 100,
  opacity: 100
}
```

## 已知問題與解決

### 1. 特效衝突
- **方案**: 即時預覽 + 提示
- 允許疊加，使用者自行判斷

### 2. 效能問題
- 過多特效警告
- CSS `will-change` 優化
- GPU 加速 `translateZ(0)`

### 3. CSS transform 順序
- 固定順序: translate → rotate → scale → skew
- 文件說明影響

### 4. 介面複雜度
- 分頁: 基礎/移動/特效
- 特效區可摺疊
- 提供預設範本

### 5. 儲存限制
- 已改用 IndexedDB
- 容量可達數百 MB ~ GB
- 無嚴格大小限制
- 超過 50MB 會提示效能警告

### 6. 時間軸同步
- 各特效獨立循環
- 可選同步週期

## 預設範本建議
1. **桌面寵物**: 隨機移動 + 無特效
2. **飄浮效果**: 緩慢上下 + 淡入淡出
3. **霓虹燈**: 靜止 + 色相旋轉 + 發光
4. **翻滾動畫**: 左→右 + 旋轉

## 技術可行性
✅ 單一 HTML
✅ 所有移動模式
✅ 特效疊加
✅ IndexedDB (支援大檔案)
✅ 透明背景
✅ 離線執行
✅ GIF 支援 (無大小限制)

## 檔案結構
```
/ImageMoving/
  ├─ config.html    (設定介面)
  ├─ result.html    (結果頁面)
  └─ DESIGN.md      (本文件)
```
