這是一份將四個子專案（前端、核心後端、LINE Profile 服務、匯率爬蟲）整合在一起的完整 **主專案 README.md**。這份文件詳細說明了整個系統的架構、各模組分工以及統一的開發與部署流程。

---

# 📚 分帳系統 (Split Bill System) - 全方位解決方案

本專案是一個功能完整的帳單拆分平台，旨在簡化團體出遊或聚餐時的帳務管理。系統整合了 **LINE 登入**、**多幣別匯率自動更新** 以及 **前後端分離架構**，提供流暢的行動端與桌面端使用體驗。

---

## 🌟 系統架構與專案分工

本系統由以下四個獨立倉庫協同運作，共同組成完整的服務生態：

| 倉庫名稱 | 角色定位 | 核心技術棧 | 主要功能 |
| --- | --- | --- | --- |
| **`split_front`** | **前端介面** | React 19, LIFF SDK | 視覺化介面、帳單管理、LINE 身份驗證整合 |
| **`split_server`** | **核心後端 API** | Node.js, Express, MySQL | 業務邏輯計算、群組與分帳記錄、匯率換算 |
| **`split_line_login_server`** | **LINE Profile 服務** | Python, Flask, SQLAlchemy | 儲存與同步 LINE 使用者頭像、名稱等資訊 |
| **`rate_Crawler`** | **匯率爬蟲工具** | Python, BeautifulSoup4 | 定期抓取台銀日圓(JPY)匯率並更新至資料庫 |

---

## 🛠️ 快速啟動指南 (Quick Start)

### 1. 前置準備

* **資料庫**: MySQL (需建立 `USER`, `GROUP_TABLE`, `BILL_RECORD`, `RATE` 等表格)。
* **環境**: Node.js 16+、Python 3.8+。
* **LINE 帳號**: 需在 [LINE Developers](https://developers.line.biz/) 申請 LIFF ID。

### 2. 獲取原始碼

```bash
git clone https://github.com/WordHanDa/split_front.git
git clone https://github.com/WordHanDa/split_server.git
git clone https://github.com/WordHanDa/split_line_login_server.git
git clone https://github.com/WordHanDa/rate_Crawler.git

```

### 3. 各服務配置說明

#### 🟢 A. 資料庫自動化：`rate_Crawler`

負責確保系統擁有最新的匯率資料（例如日圓換算）。

1. 進入目錄並安裝依賴：`pip install -r requirements.txt`。
2. 設定 `.env` 中的資料庫資訊。
3. 執行測試：`python -c "from api.main import fetch_rate; print(fetch_rate())"`。
4. **註**：部署於 Vercel 時可設定 Cron Job 每小時自動執行。

#### 🔵 B. 身分同步：`split_line_login_server`

負責管理 LINE 使用者的個人資料。

1. 進入目錄並安裝依賴：`pip install -r requirements.txt`。
2. 配置環境變數後執行：`python db.py`。
3. 提供 API 端點如 `GET /api/profile/<user_id>`。

#### 🟡 C. 核心大腦：`split_server`

處理所有帳單運算與資料儲存。

1. 進入目錄並安裝依賴：`npm install`。
2. 配置 `.env` 中的 `dbhost`, `dbuser`, `dbpassword`。
3. 啟動服務：`npm run dev` (監聽連接埠 3002)。

#### 🔴 D. 使用者入口：`split_front`

1. 進入目錄並安裝依賴：`npm install`。
2. 設定 `.env`：
* `REACT_APP_LIFF_ID`: 您的 LIFF ID。
* `REACT_APP_API_URL`: 指向 `split_line_login_server` 的 URL。


3. 啟動前端：`npm start` (監聽連接埠 3000)。

---

## 📊 資料流與功能亮點

1. **身分驗證**: 使用者開啟前端時，透過 LINE LIFF 登入，資訊會由 `split_line_login_server` 儲存並維護。
2. **匯率自動化**: `rate_Crawler` 會定時從台灣銀行抓取匯率，並將匯率乘以 10,000 以整數存入資料庫（避免浮點數誤差），供 `split_server` 進行帳單換算。
3. **精準拆帳**: `split_server` 支援「百分比」或「具體項目」等多種分帳模式。
4. **跨平台支援**: 前端採用響應式設計，完美適配手機與電腦瀏覽器。

---

## ☁️ 部署資訊 (Vercel Integration)

本專案全系列支援 **Vercel** 部署：

* **前端**: 靜態部署。
* **API (Node/Python)**: 透過 `vercel.json` 配置作為 Serverless Functions 運行。
* **定時任務**: `rate_Crawler` 可配合 Vercel Cron Job 定期觸發更新。
* 說明影片：https://youtu.be/W9dQ3CPfRN4

