# 115thFP
-----

# 分帳系統 (Split Bill System)

本專案是一個完整的、前後端分離的帳單拆分解決方案，旨在幫助團體輕鬆管理和結算共同支出的帳單。系統特別整合了 **LINE 登入** 作為身份驗證方式，並支援多幣別匯率轉換。

-----

## 🌟 專案結構與功能概覽

本系統由三個主要 Git 倉庫協同運作：

| 倉庫名稱 | 角色定位 | 核心技術棧 | 主要功能 |
| :--- | :--- | :--- | :--- |
| **`split_front`** | **前端介面** | React 19, LINE LIFF SDK | 帳單、群組管理介面、LINE 身份驗證 |
| **`split_server`** | **核心後端 API** | Node.js (Express.js), MySQL | 用戶、群組、帳單記錄、費用分割計算、匯率支援 |
| **`split_line_login_server`** | **LINE Profile 服務** | Python (Flask), SQLAlchemy, MySQL | 儲存與查詢 LINE 使用者的個人 Profile 資訊 |

-----

## 🛠️ 快速啟動指南 (Quick Start)

要完整運行整個專案，您需要依序配置和啟動三個服務。

### 1\. 前置準備 (Prerequisites)

  * **資料庫**: MySQL 或 MySQL-compatible DB。
  * **後端環境**: Node.js 16+ (用於 `split_front` 和 `split_server`)。
  * **Python 環境**: Python (用於 `split_line_login_server`)。
  * **LINE LIFF**: 需在 LINE Developers Console 註冊 LIFF 應用程式以取得 ID。

### 2\. 複製專案 (Cloning Repositories)

請分別複製這三個倉庫：

```bash
git clone https://github.com/WordHanDa/split_front.git
git clone https://github.com/WordHanDa/split_server.git
git clone https://github.com/WordHanDa/split_line_login_server.git
```

### 3\. 配置與啟動服務

#### A. 啟動 `split_line_login_server` (Python / Flask)

此服務用於儲存 LINE Profile 資料，並作為前端認證 API 的預設端點。

1.  進入目錄並安裝依賴：
    ```bash
    cd split_line_login_server
    python -m venv .venv
    source .venv/bin/activate
    pip install -r requirements.txt
    ```
2.  設定資料庫環境變數（建立 `.env` 檔案或使用 `export`）：
      * `DB_USER`
      * `DB_PASSWORD`
      * `DB_HOST`
      * `DB_NAME`
3.  啟動服務：
    ```bash
    python db.py
    # 服務監聽在 http://0.0.0.0:5001
    ```

#### B. 啟動 `split_server` (Node.js / Express)

此為核心業務邏輯 API 服務器，處理帳單、群組和計算。

1.  進入目錄並安裝依賴：
    ```bash
    cd split_server
    npm install
    ```
2.  設定資料庫環境變數（建立 `.env` 檔案）：
      * `dbhost`
      * `dbuser`
      * `dbpassword`
3.  **資料庫準備**: 確保 MySQL 資料庫已建立並包含 `USER`、`GROUP_TABLE`、`BILL_RECORD` 等表格。
4.  啟動服務：
    ```bash
    npm run dev
    # 服務器將在 http://localhost:3002 上運行
    ```

#### C. 啟動 `split_front` (React)

此為使用者介面，透過 LINE LIFF SDK 進行身份驗證。

1.  進入目錄並安裝依賴：
    ```bash
    cd split_front
    npm install
    ```
2.  設定環境變數（建立 `.env` 檔案）：
    ```env
    REACT_APP_LIFF_ID=your_line_liff_id # e.g., 2007317887-Dq8Rorg5
    # 設定為步驟 A 啟動的 LINE Profile API 端點
    REACT_APP_API_URL=http://localhost:5001/api/profile 
    ```
3.  啟動開發伺服器：
    ```bash
    npm start
    # 應用程式將在 http://localhost:3000 運行
    ```

-----

## 核心後端 API (`split_server`) 端點摘要

核心 API 支援多種操作，主要分類如下：

  * **用戶管理**: `/USER`, `/createUser`, `/updateUser`
  * **群組管理**: `/GROUP`, `/createGroup`, `/updateGroupSettle`, `/api/groups/:groupId`
  * **帳單管理**: `/createBill`, `/updateBill`, `/getBillsByGroupId`
  * **統計計算**: `/getGroupTotals`, `/group_balance` (獲取群組餘額)
  * **匯率管理**: `/RATE` (獲取最新匯率), `/YOUR_RATE/latest` (獲取群組內最新匯率)

-----

## LINE Profile 服務 (`split_line_login_server`) API 摘要

此 API 提供 LINE 用戶 Profile 的 CRUD 操作：

  * `GET /api/profiles`：取得所有 profiles
  * `GET /api/profile/<user_id>`：依 `UserID` 取得單一 profile
  * `POST /api/profile`：建立或更新單一 profile (JSON body 需包含 `userId` 和 `displayName`)
  * `POST /api/profiles/batch`：批次建立或更新 profiles

-----

## ☁️ 部署資訊 (Deployment)

所有三個專案都已準備好或支援部署到 **Vercel** 平台。

  * **`split_line_login_server`**: 使用 `@vercel/python` builder，`vercel.json` 已配置將所有請求轉發到 `db.py`。
  * **`split_server`**: 支援 Vercel 部署，請參考 `vercel.json` 檔案設定。
  * **`split_front`**: 建議將 `build/` 目錄的內容部署到 Vercel、Netlify 或其他靜態網站託管服務。

在 Vercel 上部署時，請務必在 Project Settings 中設定所有必要的環境變數（DB 連線資訊、LIFF ID 等）。
