# 🐷 Taiwan Pork Detector

> 拍下食品包裝，AI 自動判讀產地是否為台灣認定的非洲豬瘟疫區，並將成分翻譯成繁體中文、標記豬肉來源。

一個單檔 HTML 的網頁應用，專為 iPhone 設計。開啟相機拍食品外包裝，透過 OpenAI 或 Google 的視覺模型進行四階段分析。

🔗 **線上使用**：[https://josephli0419.github.io/Taiwan_pork_detector/](https://josephli0419.github.io/Taiwan_pork_detector/)

---

## ✨ 功能

1. **📸 開啟相機拍攝**：直接呼叫 iPhone 後鏡頭，或從相簿選圖
2. **🌏 產地疫區比對**：AI 辨識產地 → 比對台灣農業部防檢署「近三年曾發生非洲豬瘟之國家（地區）一覽表」
3. **📝 成分翻譯**：將多國語言的食品成分（英、日、韓、法、德、俄、西等）翻譯為繁體中文
4. **🥓 豬肉成分偵測**：多語言關鍵字掃描（豬、豚、pork、ham、bacon、lard、gelatin、prosciutto、돼지、豚肉、свинина 等）
5. **⚖️ 綜合判定**：給出「高風險 / 需留意 / 含豬肉 / 低風險」四級判定

## 🧪 分析流程（LangGraph 式 Pipeline）

```
[拍照]
   ↓
[Node 01] 擷取產品資訊（產品名、產地、成分原文）
   ↓
[Node 02] 查核非洲豬瘟疫區（比對內建清單）
   ↓
[Node 03] 翻譯成分為繁體中文（拆解成陣列）
   ↓
[Node 04] 掃描豬肉關鍵字
   ↓
[最終判定]
```

## 🤖 支援的 AI 模型

| 提供者 | 推薦模型 | 說明 |
|---|---|---|
| **OpenAI** | `gpt-5.1-mini` | Reasoning 模型，支援視覺，便宜快速 |
| **OpenAI** | `gpt-5.1` | 更強但更貴 |
| **Google** | `gemma-4-26b-a4b-it` | 26B MoE 開源模型，Google AI Studio 可免費使用 |
| **Google** | `gemini-2.5-flash` | Google 閉源模型，若 Gemma 不穩可改用 |

## 🚀 使用方法

### 線上版（推薦）
1. iPhone Safari 打開 [https://josephli0419.github.io/Taiwan_pork_detector/](https://josephli0419.github.io/Taiwan_pork_detector/)
2. 右上角齒輪 ⚙️ → 選擇提供者 → 輸入 API 金鑰 → 儲存
3. 點「開啟相機」拍食品包裝
4. 點「開始分析」

### 加到主畫面像 App
Safari 打開後 → 下方分享鈕 → 「加到主畫面」，桌面就會出現圖示，點開跟原生 App 一樣全螢幕。

### 本機使用
```bash
git clone https://github.com/JosephLi0419/Taiwan_pork_detector.git
cd Taiwan_pork_detector
# 用 Python 起一個本機 HTTPS server（相機需要 HTTPS）
python3 -m http.server 8000
# 或直接把 index.html 部署到 Netlify / Vercel / Cloudflare Pages
```

> ⚠️ **注意**：直接用 `file://` 開啟 HTML 檔會被 Safari 擋掉相機權限，必須透過 HTTP(S) 伺服器。

## 🔑 取得 API 金鑰

### OpenAI
1. 登入 [platform.openai.com/api-keys](https://platform.openai.com/api-keys)
2. 點 **Create new secret key**
3. 複製以 `sk-` 開頭的金鑰

### Google（Gemma / Gemini）
1. 登入 [aistudio.google.com/apikey](https://aistudio.google.com/apikey)
2. 點 **Create API key**
3. 複製以 `AIza` 開頭的金鑰
4. Gemma 4 目前有慷慨的免費額度

## 🔒 隱私說明

- **API 金鑰只存在 `sessionStorage`**：關閉分頁即清除，不會上傳到任何伺服器
- **圖片直接送到你選擇的 AI 提供者**：本專案沒有後端，所有請求從瀏覽器直接打到 OpenAI / Google
- **本網站不收集任何使用資料**

## 📋 非洲豬瘟疫區清單

內建約 80 個國家與地區的中英文對照，資料來源：
- [農業部防檢署 · 非洲豬瘟資訊專區](https://asf.aphia.gov.tw/)
- WOAH（世界動物衛生組織）WAHIS 疫情通報系統
- 含 2024–2025 新增疫區（蒙特內哥羅、科索沃、阿爾巴尼亞、安哥拉、加彭、西班牙等）

⚠️ 此清單為開發時的快照，實際違規攜帶裁罰請以[防檢署官方公告](https://asf.aphia.gov.tw/)為準。

## 🛠 技術棧

- **前端**：單檔 HTML + 原生 JavaScript（無框架）
- **樣式**：CSS Variables + Google Fonts（Noto Serif TC / Bodoni Moda / JetBrains Mono）
- **圖片處理**：Canvas API 壓縮到 1280px JPEG（降低 token 用量）
- **API**：OpenAI Chat Completions / Google Gemini API (`generateContent`)
- **無後端、無構建工具**：純靜態，可部署到任何靜態主機

## ⚠️ 免責聲明

本工具為輔助判斷工具，**不是官方檢驗報告**。

- AI 辨識可能有誤判（OCR 錯誤、遮擋、反光等）
- 成分翻譯可能不精確
- 加工食品中的明膠、油脂等可能含豬源但未明確標示
- 實際入境裁罰、食品安全判斷請以[台灣農業部防檢署](https://www.aphia.gov.tw/)官方資訊為準

## 📄 License

MIT

## 🙋 貢獻

歡迎提 Issue 或 PR！特別歡迎：
- 疫區清單更新
- 豬肉關鍵字語言擴充
- UI / UX 改進
