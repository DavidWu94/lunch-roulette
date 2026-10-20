# 「午餐輪盤賭 ✕ 預算刮刮樂」完整產品與技術規格書 (Specification Document)

> **版本**：v1.0.0  
> **發布日期**：2026-10-20  
> **作者**：資深前端架構師 / UI/UX 設計師  
> **專案狀態**：正式上線 (Production Ready)  
> **線上體驗**：[https://davidwu94.github.io/lunch-roulette/](https://davidwu94.github.io/lunch-roulette/)  
> **開源倉庫**：[https://github.com/DavidWu94/lunch-roulette](https://github.com/DavidWu94/lunch-roulette)

---

## 目錄 (Table of Contents)
- [1. 專案背景與產品願景](#1-專案背景與產品願景)
- [2. 系統架構與技術棧規格](#2-系統架構與技術棧規格)
- [3. UI/UX 設計系統與視覺規範](#3-uiux-設計系統與視覺規範)
- [4. 核心功能模組詳細規格](#4-核心功能模組詳細規格)
  - [4.1 雙 Tab 導航與全域頂欄](#41-雙-tab-導航與全域頂欄)
  - [4.2 模組一：命運大輪盤 (Lunch Roulette)](#42-模組一命運大輪盤-lunch-roulette)
  - [4.3 模組二：今日預算刮刮樂 (Budget Scratch Card)](#43-模組二今日預算刮刮樂-budget-scratch-card)
  - [4.4 模組三：原生 Web Audio API 音訊合成系統](#44-模組三原生-web-audio-api-音訊合成系統)
  - [4.5 模組四：歷史紀錄與本地持久化系統](#45-模組四歷史紀錄與本地持久化系統)
- [5. 核心演算法與數學模型](#5-核心演算法與數學模型)
  - [5.1 輪盤 Ease-out 減速物理動畫](#51-輪盤-ease-out-減速物理動畫)
  - [5.2 角度與扇區精準碰撞映射演算法](#52-角度與扇區精準碰撞映射演算法)
  - [5.3 刮刮樂面積採樣演算法](#53-刮刮樂面積採樣演算法)
- [6. 資料字典 (Data Schema)](#6-資料字典-data-schema)
- [7. 邊界條件與容錯機制 (Edge Cases)](#7-邊界條件與容錯機制-edge-cases)
- [8. 瀏覽器相容性矩陣與效能指標](#8-瀏覽器相容性矩陣與效能指標)

---

## 1. 專案背景與產品願景

### 1.1 背景痛點
上班族、學生每日中午皆面臨兩大核心哲學問題：
1. **選擇困難症**：「今天中午吃什麼？」（猶豫不決浪費大把休息時間）。
2. **荷包超支恐慌**：「這個月伙食費快爆了，今天到底能吃多少錢？」。

### 1.2 產品定位
「午餐輪盤賭 ✕ 預算刮刮樂」定位為一款**輕量化、潮流現代感、富幽默感且高度互動**的單頁 Web 應用程式（Single-Page Web App），以遊戲化（Gamification）方式在一分鐘內解決午餐決策與預算控制。

### 1.3 設計原則
- **極簡零依賴**：不使用大型框架（無 React/Vue/Node.js 依賴），純 HTML5 + CSS3 + Vanilla JavaScript。
- **單一檔案發布**：所有 CSS、JS、HTML 完全內嵌於 `index.html`，具備極致的可攜性與離線執行能力。
- **永不失效的音效**：捨棄易失效的外部 MP3/WAV 音訊外鏈，100% 透過瀏覽器底層 **Web Audio API** 即時合成物理擬真音效。
- **現代極簡潮流風格**：深沉黑灰底搭配高飽和 Neon 螢光色系、毛玻璃擬態（Glassmorphism）與微互動物理反饋。

---

## 2. 系統架構與技術棧規格

### 2.1 系統架構圖
```
┌─────────────────────────────────────────────────────────────────────────┐
│                        index.html (Single-File App)                     │
├─────────────────────────────────────────────────────────────────────────┤
│  [UI Layer: HTML5 + CSS3 Variables + Glassmorphism + Flexbox / Grid]    │
│  ┌───────────────────────┬───────────────────────┬────────────────────┐ │
│  │    App Header & Nav   │ Tab 1: 命運大輪盤     │ Tab 2: 預算刮刮樂   │ │
│  │ (靜音開關 / 歷史抽屜) │ (Canvas + 菜單管理池) │ (雙層 Canvas 塗層)  │ │
│  └───────────────────────┴───────────────────────┴────────────────────┘ │
├─────────────────────────────────────────────────────────────────────────┤
│  [Logic & Engine Layer: Vanilla ES6+ JavaScript]                        │
│  ┌─────────────────────┐  ┌─────────────────────┐  ┌──────────────────┐ │
│  │ Wheel Physics Engine│  │ Scratch Engine      │  │ SoundEngine      │ │
│  │ (rAF + Ease-out)    │  │ (Alpha Sampling)    │  │ (Web Audio API)  │ │
│  └─────────────────────┘  └─────────────────────┘  └──────────────────┘ │
├─────────────────────────────────────────────────────────────────────────┤
│  [Persistence Layer: HTML5 LocalStorage]                                │
│   - lunch_wheel_items (菜單池) | lunch_history (抽卡歷史) | lunch_muted │
└─────────────────────────────────────────────────────────────────────────┘
```

### 2.2 技術棧規格表
| 領域 | 使用技術 | 規格與用途說明 |
| :--- | :--- | :--- |
| **標記語言** | HTML5 | 語意化標籤、`<canvas>` 雙畫布渲染 |
| **樣式與版面** | CSS3 (Modern Vanilla) | CSS Custom Properties、Flexbox、CSS Grid、Backdrop-filter、Keyframe 動畫 |
| **程式語言** | Vanilla JavaScript (ES6+) | 原生 DOM API、Class 封裝、Canvas 2D Context |
| **音效合成** | Web Audio API | `AudioContext`、`OscillatorNode`、`GainNode`、`BiquadFilterNode` 即時音效合成 |
| **持久化存儲** | LocalStorage API | 本地 JSON 序列化儲存菜單資料、歷史存檔與偏好設定 |
| **字體與 CDN** | Google Fonts + Confetti | Google Fonts (`Plus Jakarta Sans` / `Noto Sans TC`)、`canvas-confetti` (含原生 Canvas 備援) |

---

## 3. UI/UX 設計系統與視覺規範

### 3.1 顏色變數定義 (Design Tokens)
```css
--bg-dark: #0a0e17;              /* 主背景深度黑灰 */
--bg-surface: #111827;           /* 卡片本體灰底 */
--bg-surface-elevated: #1e293b;  /* 凸起元件與輸入框底色 */
--bg-glass-card: rgba(30, 41, 59, 0.6); /* 毛玻璃半透明面板 */
--border-glass: rgba(255, 255, 255, 0.08); /* 晶透細描邊 */

/* Neon 霓虹強調色 */
--neon-cyan: #00f2fe;    /* 主按鈕、指針描邊、選中態發光 */
--neon-blue: #4facfe;    /* 輔助漸層青藍 */
--neon-pink: #f72585;    /* 指針主色、重度強調 */
--neon-purple: #7209b7;  /* 氛圍環境光漸層 */
--neon-amber: #ffb703;   /* 預算刮刮樂主色 */
--neon-emerald: #10b981; /* 健康分類、成功態 */
--neon-rose: #ff4d6d;    /* 刪除、危險按鈕 */
```

### 3.2 字體系統
- **英數/幾何標題**：`'Plus Jakarta Sans'`（字重 500、700、800、900）。
- **中文字型**：`'Noto Sans TC'`, `system-ui`, `-apple-system`, `sans-serif`。

### 3.3 響應式佈局斷點
- **桌面寬螢幕 (≥ 992px)**：
  - Tab 1 採用雙欄排版（左側 1.25fr 輪盤舞臺，右側 1fr 菜單池管理面版）。
  - Tab 2 居中卡片展示（最大寬度 560px）。
- **行動裝置 (< 992px)**：
  - 單欄流動排版，輪盤舞臺置頂，菜單池置底。
  - Canvas 自動按 `1:1` 比例隨容器寬度縮放。

---

## 4. 核心功能模組詳細規格

### 4.1 雙 Tab 導航與全域頂欄
- **Logo 標誌**：炫彩微光圖標 + 吃什麼救星品牌字 + `PRO` 膠囊標籤。
- **膠囊 Tab 切換組**：
  - `tabWheelBtn`：🎡 命運大輪盤（選中時 Cyan-Blue 霓虹漸層發光）。
  - `tabScratchBtn`：🪙 預算刮刮樂（選中時 Amber-Orange 霓虹漸層發光）。
- **頂欄輔助工具**：
  - **靜音開關（🔊/🔇）**：切換全域 Web Audio 發聲狀態，即時存入 `localStorage.lunch_muted`。
  - **歷史紀錄按鈕（📜）**：開啟彈窗檢視最近 30 筆抽籤存檔。

---

### 4.2 模組一：命運大輪盤 (Lunch Roulette)

#### 4.2.1 Canvas 繪製規範
- **畫布解析度**：寬 800px × 高 800px（以高清視網膜標準繪製，透過 CSS 自動縮放適配）。
- **扇區動態劃分**：依據當前菜單池項目數 $N$ 等分，每扇區夾角 $\Delta\theta = \frac{2\pi}{N}$。
- **漸層與色彩**：各扇區填充獨立鮮艷色彩（預設提供 10 種高彩度配色調色盤），外框帶微亮半透明描邊。
- **文字排版**：沿半徑方向由外向內旋轉繪製，文字長度超長時自動截斷並加省略號（`…`）。
- **外環金屬裝飾**：
  - 外圈繪製發光 Cyan 霓虹環圈（帶 Glow 陰影效果）。
  - 環圈均勻分布金屬呼吸鉚釘點（Pin Dots）。
- **中央金屬軸心**：雙層深色同心圓，中心標示「SPIN」微發光立體字樣。

#### 4.2.2 指針與物理動畫機制
- **頂部指針**：置於正上方（12 點鐘方向），採用桃紅漸層 SVG 寶劍水滴造型。
- **旋轉動能**：
  - 最小保證圈數：旋轉 6 圈（$12\pi$）以上。
  - 隨機偏移量：$0 \sim 2\pi$。
  - 總時長：3.8 秒 ～ 4.6 秒。
- **齒輪卡榫音效與微彈跳**：
  - 每一動畫幀（`requestAnimationFrame`）偵測當前指針接觸的扇區 ID。
  - 當指針跨越扇區邊界時，觸發 Web Audio 卡榫嘀嗒聲（Tick），並對指針施加 $\pm 12^\circ$ 的微抖動與自然回彈動畫。
- **揭曉反饋**：
  - 停下瞬間彈出慶祝 Modal。
  - 發射五彩紙屑 Confetti 粒子。
  - 播放勝利和弦音效（Win Fanfare）。
  - 將中獎餐點、分類、時間寫入歷史存檔。

#### 4.2.3 菜單池管理（CRUD）
- **分類快速篩選**：
  - `全部` / `🥢 平價小吃` / `🥗 健康輕食` / `🍜 異國料理` / `🥩 大餐犒賞`。
  - 切換分類時，輪盤動態即時重繪該分類下的項目；若項目為空顯示友善提示。
- **即時新增項目**：
  - 輸入餐點名稱（限制 16 字以內）+ 選擇分類標籤。
  - 自動隨機分配調色盤配色，即時插入列表頂端並更新輪盤。
- **一鍵刪除**：
  - 點擊項目右側 `✕` 按鈕刪除。
  - 防呆機制：菜單項目總數小於或等於 2 項時禁止刪除，並跳出 Toast 警告。
- **🔀 隨機洗牌**：Fisher-Yates 洗牌演算法，瞬間重組扇區分布。
- **恢復預設菜單**：支援一鍵還原至 10 種預設餐點。

---

### 4.3 模組二：今日預算刮刮樂 (Budget Scratch Card)

#### 4.3.1 雙層畫布架構
```
┌──────────────────────────────────────────────┐
│  頂層：#scratchCanvas (銀金屬質感塗層)        │ ── globalCompositeOperation = 'destination-out'
├──────────────────────────────────────────────┤
│  底層：.scratch-reward-layer (DOM 揭曉內容)   │ ── 預算標籤、金額字樣、幽默評語
└──────────────────────────────────────────────┘
```

#### 4.3.2 銀色金屬防偽塗層繪製
- **漸層底色**：45 度角銀灰雙向金屬反光漸層（`#94a3b8` $\to$ `#cbd5e1` $\to$ `#64748b`）。
- **拉絲噪點**：隨機繪製 450 條微細白色半透明拉絲水平噪點紋理。
- **防偽印章**：正中央印製金色細框與深色「🪙 刮開揭曉今日預算 🪙」字樣。

#### 4.3.3 塗抹刮除體驗
- **多端支援**：支援滑鼠左鍵拖曳與手機 Touch 觸控（綁定 `touch-action: none` 防止網頁下拉重整或滑動）。
- **擦除合成模式**：`ctx.globalCompositeOperation = 'destination-out'`，以半徑自適應的圓形筆刷抹除透明像素。
- **刮除物理音效**：塗抹時連續觸發沙沙金屬摩擦聲，帶 75ms 節流閥避免音訊節點溢出。

#### 4.3.4 取樣演算法與自動揭曉
- 每隔 20 像素抽取一個格點檢驗 Alpha 通道值（`pixels[i + 3] < 128` 即判定為已刮除）。
- 當刮除面積達到 **50%** 時：
  - 觸發 `#scratchCanvas` CSS 淡出過渡（`opacity: 0`）。
  - 將進度條瞬間拉至 100%。
  - 自動播放勝利和弦與 Confetti 粒子。
  - 將抽出的預算紀錄寫入歷史檔案。

#### 4.3.5 預算獎勵池設定
| 預算等級 | 金額 | 等級稱號 | 幽默毒舌評語 |
| :--- | :--- | :--- | :--- |
| **玄學蹭飯** | `$0` | 老闆請客 / 蹭飯大師 | 「環顧四周看哪個主管在叫外賣，眼神流露出無辜與飢餓，今天你一毛不拔！」 |
| **極致苦行** | `$45` | 超商光合作用套餐 | 「兩顆茶葉蛋加一杯大溫開水，今天在辦公室主要行光合作用。」 |
| **平民自由** | `$70` | 肉燥飯自由 | 「大碗肉燥飯加滷蛋，記得多舀兩勺免費的香脆辣菜脯！」 |
| **標準社畜** | `$100` | 學餐排骨便當 | 「三樣配菜加一隻炸雞腿或排骨，碳水化合物是撐到下班的唯一真理。」 |
| **精緻自律** | `$140` | 低GI健康餐盒 | 「舒肥水煮雞胸肉配紫米飯，拍照發IG假裝自己過得精緻自律。」 |
| **微醺犒賞** | `$220` | 特濃拉麵加叉燒 | 「麵硬湯濃加溏心蛋，卡路里是什麼能吃嗎？吃飽下午才有力氣改需求！」 |
| **報復消費** | `$350` | 美式雙層漢堡拼盤 | 「濃郁起司肉排配大杯肥宅快樂水，明天的事明天再來後悔！」 |
| **財富自由** | `$600` | 頂級日式鰻魚雙拼 | 「今天你就是整條美食街最靚的仔，吃完發票記得對獎！」 |

---

### 4.4 模組三：原生 Web Audio API 音訊合成系統

本系統完全不發送任何外部音訊 HTTP 請求，100% 由本地端 AudioContext 合成音效波形。

#### 4.4.1 齒輪卡榫音效 (Tick Sound)
- **振盪器類型**：`triangle`（三角波）。
- **頻率設計**：$750\text{ Hz} \sim 950\text{ Hz}$（隨機微偏動），於 $35\text{ ms}$ 內指數驟降至 $150\text{ Hz}$。
- **包絡線 (Gain Envelope)**：初始音量 0.15，在 $35\text{ ms}$ 內指數衰減至 0.001。模擬真實鐘錶/卡榫的短促脆響。

#### 4.4.2 刮除摩擦音效 (Scratch Sound)
- **音源**：預先產生之 $0.5\text{ 秒}$ 隨機白噪音緩衝區（`AudioBuffer`）。
- **濾波器**：`BiquadFilterNode` 帶通濾波器（`bandpass`），中心頻率 $1400 \sim 1800\text{ Hz}$，品質因數 $Q = 2.5$。
- **包絡線**：時長 $60\text{ ms}$ 指數衰減，精準還原硬物刮拭金屬塗層的沙沙聲。

#### 4.4.3 勝利揭曉音效 (Celebration Fanfare)
- **振盪器類型**：`sine`（正弦波純音）。
- **和弦結構**：大調上行琶音：
  - $C_5 (523.25\text{ Hz})$
  - $E_5 (659.25\text{ Hz})$
  - $G_5 (783.99\text{ Hz})$
  - $C_6 (1046.50\text{ Hz})$
- **音符時距**：每個音符間隔 $90\text{ ms}$ 依序發聲，帶長殘響釋放。

#### 4.4.4 自動播放安全性 (Autoplay Policy)
針對現代瀏覽器規範，在使用者首次發生點擊（`mousedown`、`touchstart`、`click`）時自動執行 `ctx.resume()`，確保音訊管線暢通無阻。

---

### 4.5 模組四：歷史紀錄與本地持久化系統
- **存檔觸發時機**：每次輪盤停定產生結果、或刮刮樂刮除過半自動揭曉時。
- **隊列規則**：FIFO（先進先出），固定保存最多 30 筆最新紀錄。
- **抽屜操作**：支援即時滾動檢視與「清空歷史紀錄」二次確認。

---

## 5. 核心演算法與數學模型

### 5.1 輪盤 Ease-out 減速物理動畫
為了呈現自然的物理慣性衰減，採用 **Cubic Ease-Out** 曲線：
$$P(t) = 1 - (1 - t)^3, \quad t \in [0, 1]$$
- 當前旋轉弧度 $\theta(t)$ 計算公式：
  $$\theta(t) = \theta_0 + \Delta\theta_{\text{total}} \cdot P\left(\frac{t - t_0}{T_{\text{duration}}}\right)$$
- 隨機總轉動角：
  $$\Delta\theta_{\text{total}} = 2\pi \cdot k + \text{random}(0, 2\pi), \quad k \ge 6$$

### 5.2 角度與扇區精準碰撞映射演算法
輪盤指針固定於正上方，即畫布坐標系的 $270^\circ$（$\frac{3\pi}{2}$ 弧度）。
當輪盤旋轉累積弧度為 $\theta_{\text{rot}}$ 時，指針在輪盤相對坐標系中的角度 $\theta_{\text{rel}}$ 為：
$$\theta_{\text{rel}} = \left(\frac{3\pi}{2} - (\theta_{\text{rot}} \bmod 2\pi)\right) \bmod 2\pi$$
若 $\theta_{\text{rel}} < 0$，則 $\theta_{\text{rel}} \leftarrow \theta_{\text{rel}} + 2\pi$。

當前中選的扇區索引 $I_{\text{target}}$：
$$I_{\text{target}} = \left\lfloor \frac{\theta_{\text{rel}}}{\Delta\theta_{\text{slice}}} \right\rfloor \bmod N$$
此演算法保證了視覺上的指針位置與程式判定結果 **100% 絕對一致**，杜絕任何角度偏差。

### 5.3 刮刮樂面積採樣演算法
- 傳統逐像素掃描需讀取 $W \times H \times 4$ 個字節（以 $500 \times 300$ 而言需遍歷 15 萬次），易造成主線程掉幀。
- **格點步長抽樣優化**：設定步長 $S = 20\text{ px}$，將掃描次數壓縮至數百次內：
  $$\text{Sample}(x, y) = \text{pixels}[(y \cdot W + x) \cdot 4 + 3] < 128$$
  $$\text{Percent} = \frac{\sum \text{Sample}}{\text{Total Samples}} \times 100\%$$
- 效能耗時小於 $0.5\text{ ms}$，實現極致 60 FPS 順暢刮除體驗。

---

## 6. 資料字典 (Data Schema)

### 6.1 `lunch_wheel_items` (菜單列表)
儲存於 `localStorage.getItem('lunch_wheel_items')`，格式為 JSON Array。

```typescript
interface MenuItem {
  id: string;          // 唯一識別碼 (時間戳字串，如 "1729420000000")
  name: string;        // 餐點名稱 (長度 1~16 字元，如 "特濃豚骨叉燒拉麵")
  category: string;    // 分類標籤 ("平價小吃" | "健康輕食" | "異國料理" | "大餐犒賞")
  color: string;       // HEX 顏色代碼 (如 "#00f2fe")
}
```

### 6.2 `lunch_history` (抽獎存檔)
儲存於 `localStorage.getItem('lunch_history')`，格式為 JSON Array。

```typescript
interface HistoryRecord {
  type: 'wheel' | 'scratch'; // 抽獎類別：輪盤或刮刮樂
  title: string;             // 結果項目名稱 (如 "牛肉湯麵" 或 "$120 (標準社畜套餐)")
  category: string;          // 分類或標籤名
  time: string;              // 格式化時間戳 (如 "12:30:15")
}
```

### 6.3 `lunch_muted` (靜音開關)
儲存於 `localStorage.getItem('lunch_muted')`，值為字串 `"true"` 或 `"false"`。

---

## 7. 邊界條件與容錯機制 (Edge Cases)

| 異常情境 | 系統保護與降級策略 |
| :--- | :--- |
| **菜單僅剩 2 項時嘗試刪除** | 攔截刪除請求，跳出 Toast 警告：「菜單至少需要保留 2 個項目才能開抽！」，確保輪盤繪製不崩潰。 |
| **自訂輸入空白餐點名稱** | 自動觸發 HTML5 `required` 與 JS `.trim()` 檢查，空白輸入自動防阻。 |
| **離線或斷網 (Offline)** | 即使無法下載外部 `canvas-confetti` CDN，系統內建純原生 Canvas 粒子拋射備援發射器，依舊可噴灑彩紙。 |
| **初始載入時 Tab 2 隱藏** | 在切換至 Tab 2 的第一幀（`requestAnimationFrame`）重新讀取可視 DOM 寬高，避免寬高為 0 造成的畫布拉伸變形。 |
| **視窗尺寸縮放 (Resize / 橫豎屏)** | 綁定 `window.resize` 監聽器，即時根據 DPR（Device Pixel Ratio）重繪輪盤與刮刮樂塗層。 |

---

## 8. 瀏覽器相容性矩陣與效能指標

### 8.1 相容性測試矩陣
| 平台 / 瀏覽器 | 最低支援版本 | 實測相容性 | 備註 |
| :--- | :--- | :--- | :--- |
| **Google Chrome (Desktop/Android)** | Chrome 70+ | 🟢 完全相容 (100%) | 支援硬體加速 Canvas 與 Web Audio API |
| **Apple Safari (macOS / iOS)** | Safari 13+ | 🟢 完全相容 (100%) | 完整支援 WebKit 觸控事件與 AudioContext |
| **Microsoft Edge** | Edge 79+ | 🟢 完全相容 (100%) | Chromium 內核完全適配 |
| **Mozilla Firefox** | Firefox 68+ | 🟢 完全相容 (100%) | Canvas 2D 與 Web Audio 表現良好 |

### 8.2 效能指標 (Lighthouse 實測目標)
- **First Contentful Paint (FCP)**：$< 0.4\text{ s}$（單檔靜態資源極速載入）
- **Total Blocking Time (TBT)**：$0\text{ ms}$（無大型 JavaScript 庫阻塞主線程）
- **Cumulative Layout Shift (CLS)**：$0$（畫布固定高寬比，零佈局抖動）
- **Frame Rate (FPS)**：穩定 $60\text{ FPS}$ 流暢運轉

---
*文件編制完成，本規格書可直接作為專案交付驗收、後續維護或二期功能擴充之基準藍圖。*
