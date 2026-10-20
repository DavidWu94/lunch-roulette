# 🎡 午餐輪盤賭 ✕ 預算刮刮樂 (Lunch Roulette & Budget Scratch)

> 吃什麼救星 ✕ 今日預算省錢小精靈！純原生技術打造的現代極簡 Dark Mode 潮流互動 Web App。

![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/Vanilla_JS-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![Web Audio API](https://img.shields.io/badge/Web_Audio_API-00F2FE?style=for-the-badge)

---

## ✨ 核心亮點

1. **🎡 命運大輪盤（吃什麼救星）**
   - **HTML5 Canvas 高清渲染**：動態計算扇區、Neon 漸層流光、外圈金屬鉚釘與中央發光軸心。
   - **自然減速物理動畫**：Ease-out Cubic 曲線轉動 3.8 ~ 4.6 秒，精準停在指針所指項目。
   - **指針物理碰撞反饋**：指針微彈跳動畫，每次劃過扇區觸發清脆卡榫滴答聲。
   - **全自訂菜單管理池**：支援快速分類篩選（平價小吃、健康輕食、異國料理、大餐犒賞）、自訂新增與一鍵刪除。

2. **🪙 今日預算刮刮樂（省錢小精靈）**
   - **擬真銀金屬塗層**：Canvas 繪製拉絲金屬紋理與防偽印章文字。
   - **跨端流暢刮除**：完整支援滑鼠拖曳與手機觸控塗抹（防止頁面滾動干擾）。
   - **面積取樣與自動揭曉**：格點取樣演算法計算刮除比例，達到 50% 即自動淡出揭曉。
   - **毒舌幽默獎勵池**：隨機抽取今日午餐花費上限（$0 蹭飯大師、$45 光合作用套餐、$100 標準社畜餐...）與搞笑評語。

3. **🔊 純原生 Web Audio API 合成音效**
   - **零外部音檔依賴**：100% 透過瀏覽器音訊上下文即時合成，永無失聯失效風險。
   - 包含：齒輪卡榫清脆嘀嗒聲（Tick）、刮刮樂白噪音沙沙聲（Scratch）、勝利揭曉和弦琶音（Fanfare）。
   - 支援全域音效開關（靜音狀態持久化儲存）。

4. **💾 本地持久化與離線可用**
   - 菜單增刪清單、抽取歷史存檔、靜音開關均存於瀏覽器 `localStorage`，重新整理不遺失。
   - 內建原生 Canvas 五彩紙屑（Confetti）備援發射器，即使無網路離線環境也能噴發慶祝粒子。

---

## 🚀 快速開始

本專案採單一檔案整合架構（Single-file Application），無須 Node.js、無須編譯建置：

```bash
# 複製倉庫
git clone https://github.com/DavidWu94/lunch-roulette.git

# 直接在瀏覽器開啟 index.html 即可使用！
```

---

## 📱 響應式支援 (RWD)

支援 Desktop 電腦桌面、平板與 iOS / Android 手機螢幕完美適配。
