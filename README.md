# 🐷 Taiwan Pork Detector

> 拍下食品包裝，判斷是否可以把這個肉製品帶回台灣。

一個單檔 HTML 的 iPhone 網頁應用，專為出國旅行的台灣人設計。結合 AI 視覺辨識、GPS 定位與台灣防檢署非洲豬瘟疫區清單，給出清楚的「可攜帶 / 需留意 / 不可攜帶」判定。

🔗 **線上使用**:[https://josephli0419.github.io/Taiwan_pork_detector/](https://josephli0419.github.io/Taiwan_pork_detector/)

---

## ✨ 三種判定結果

| 判定 | 顏色 | 情境 |
|---|---|---|
| 🟢 **可攜帶** | 綠色 | 不含豬肉;或含豬肉但屬高溫滅菌罐頭且你在非疫區 |
| 🟡 **需留意** | 黃色 | 含豬肉 + 非疫區，但屬生鮮/加工肉品（火腿、香腸等）→ 建議主動申報 |
| 🔴 **不可攜帶** | 紅色 | 含豬肉 + 你目前在非洲豬瘟疫區 → 第 1 次罰 20 萬，第 2 次罰 100 萬 |

## 🧪 三階段分析 Pipeline

```
[拍照]
   ↓
[01] 辨識成分    AI 視覺辨識 + 翻譯成繁中 + 判斷豬肉類型
   ↓
[02] 查核疫區    GPS 定位 → 反查國家 → 比對台灣疫區清單
   ↓
[03] 檢測豬肉    AI 判斷:Yes / No / Uncertain + 理由
   ↓
[判定]          可攜帶 / 需留意 / 不可攜帶
```

## 🔍 判定依據

根據台灣農業部防檢署《動物傳染病防治條例》與現行規定:

### 疫區國家（一律禁止）
- 所有豬肉製品都不能帶
- 即使是罐頭也不行
- 違規罰鍰:第 1 次 20 萬元，第 2 次以上 100 萬元

### 非疫區國家（看類型）
- ✅ **高溫滅菌罐頭**（金屬罐、玻璃罐、軟式罐頭/retort pouch）→ 可帶
- ⚠️ **生鮮/冷藏/冷凍/熟食/煙燻/風乾肉**（火腿、香腸、肉乾、月餅含肉餡等）→ 需檢疫合格，建議主動申報
- 注意:真空包 ≠ 罐頭

## 🤖 支援的 AI 模型

在設定頁面可透過下拉選單切換:

### Google（Gemini API）
- `gemma-4-26b-a4b-it` — Gemma 4 26B MoE，開源
- `gemma-4-31b-it` — Gemma 4 31B Dense，更強但較慢

### OpenAI
- `gpt-5.1-mini` — 輕量版，速度快、費用低
- `gpt-5.1` — 完整版，更精準

Thinking mode 預設已關閉以加速推理:
- Gemini 的 `thinkingConfig.thinkingBudget = 0`
- OpenAI 的 `reasoning_effort = "none"`

## 🚀 使用方法

### 線上版（推薦）
1. iPhone Safari 打開 [https://josephli0419.github.io/Taiwan_pork_detector/](https://josephli0419.github.io/Taiwan_pork_detector/)
2. **允許定位權限**（程式會自動取得你目前所在國家）
3. 右上角齒輪 ⚙️ → 選擇提供者 → 從下拉選單選模型 → 輸入 API 金鑰 → 儲存
4. 點「開啟相機」拍食品成分表
5. 點「開始分析」，等 AI 回應（通常 5–15 秒）

### 加到主畫面像 App
Safari 打開後 → 下方分享鈕 → 「加到主畫面」，桌面會出現圖示，全螢幕使用。

### 本機開發
```bash
git clone https://github.com/JosephLi0419/Taiwan_pork_detector.git
cd Taiwan_pork_detector
python3 -m http.server 8000
# 然後開 http://localhost:8000
```

> ⚠️ 相機與定位 API **必須 HTTPS 才能使用**。用 `file://` 開啟或部署到非 HTTPS 環境會失敗。GitHub Pages、Netlify、Vercel 等平台皆為 HTTPS。

## 🔑 取得 API 金鑰

### Google（Gemma / Gemini）
1. 登入 [aistudio.google.com/apikey](https://aistudio.google.com/apikey)
2. 點 **Create API key**
3. 複製以 `AIza` 開頭的金鑰
4. 有慷慨的免費額度

### OpenAI
1. 登入 [platform.openai.com/api-keys](https://platform.openai.com/api-keys)
2. 點 **Create new secret key**
3. 複製以 `sk-` 開頭的金鑰

## 🌍 如何定位

```
iPhone GPS → 經緯度 → Nominatim API → 國家代碼 → 程式內建對照表 → 繁中國名
```

1. `navigator.geolocation.getCurrentPosition()` 向 iPhone 要 GPS 座標
2. 送到 [OpenStreetMap Nominatim](https://nominatim.openstreetmap.org/) 反查國家（免費、不需 API Key）
3. 拿到 ISO 代碼（如 `TW`、`VN`）轉成繁中國名
4. 比對內建的疫區清單，判斷你是否在疫區

## 🔒 隱私說明

- **API 金鑰**:只存在瀏覽器 `sessionStorage`，關閉分頁即清除，不會上傳
- **圖片**:直接從你瀏覽器送到 OpenAI / Google，沒有經過任何中間伺服器
- **位置**:經緯度送到 Nominatim（OpenStreetMap 基金會）換取國家資訊，不儲存
- **本網站沒有後端**，所有處理都在你的瀏覽器中進行

## 📋 疫區清單

內建約 80 個國家/地區的中英文對照，主要資料來源:
- [農業部防檢署 · 非洲豬瘟資訊專區](https://asf.aphia.gov.tw/)
- WOAH 世界動物衛生組織 WAHIS 通報系統
- 含 2024–2025 新增疫區（蒙特內哥羅、科索沃、阿爾巴尼亞、安哥拉、加彭、西班牙等）

⚠️ 清單為快照，實際以[防檢署官方公告](https://asf.aphia.gov.tw/)為準。

## 🛠 技術棧

- **純前端**:單檔 HTML + 原生 JavaScript，無框架、無構建工具
- **樣式**:CSS Variables + Google Fonts（Noto Serif TC / Bodoni Moda / JetBrains Mono）
- **AI API**:
  - OpenAI Chat Completions（結構化 JSON）
  - Google Generative Language API（`responseSchema` 結構化輸出）
- **定位**:`navigator.geolocation` + OpenStreetMap Nominatim
- **圖片壓縮**:Canvas API 自動壓到 800px JPEG 75%（前端顯示用 1280px 高品質版）

## ⚡ 效能優化

- **圖片雙版本**:前端預覽用 1280px 高品質、送 AI 用 800px 低品質（降低 token）
- **一次呼叫完成**:OCR + 翻譯 + 豬肉判斷合併為單次 API 請求
- **關閉 thinking mode**:避免不必要的推理延遲
- **90 秒 timeout**:避免無限等待
- **進度顯示**:即時顯示經過秒數

## ⚠️ 免責聲明

本工具為**輔助判斷工具**，不是官方檢驗報告。

- AI 辨識可能有誤判（OCR 錯誤、反光、遮擋等）
- 判斷結果僅供參考，實際入境攜帶請依[防檢署規定](https://asf.aphia.gov.tw/)為準
- 不確定時建議主動至機場「紅線櫃檯」申報檢疫——**主動申報最多扣留丟棄，不會被罰;不申報被抓則罰 20 萬起跳**

## 📄 License

MIT

## 🙋 貢獻

歡迎 Issue 或 PR！特別歡迎:
- 疫區清單更新
- UI / UX 改進
- Prompt 優化讓 AI 判斷更準
- 模型清單擴充（修改 `MODEL_OPTIONS` 物件）

---

**守護台灣豬，就是守護台灣傳統美食。**
