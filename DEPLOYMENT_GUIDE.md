# GitHub Pages 部署指南

## 概述

此文件說明如何將「忠茶·會員價值分析儀」系統部署到 GitHub Pages，並確保純前端功能正常運作。

## 系統架構

### 修改後的架構
1. **單頁應用 (SPA) 設計**：使用 Hash 路由系統 (`#dashboard`, `#trends`, `#marketing`, `#batch`)
2. **動態內容載入**：使用 JavaScript `fetch()` 動態載入各頁面內容
3. **會話管理**：使用 `sessionStorage` 管理登入狀態
4. **GitHub Pages 兼容**：處理相對路徑和 404 重定向

### 主要檔案
- `index.html` - 主應用程式入口點（包含登入頁面和 SPA 路由）
- `404.html` - GitHub Pages SPA 路由處理器
- `_8/code.html` - 儀表板頁面
- `_2/code.html` - 趨勢報告頁面
- `_1/code.html` - 精準行銷頁面
- `_3/code.html` - 批次數據導入頁面
- `_6/code.html`, `_7/code.html` - 分析儀頁面

## 部署步驟

### 步驟 1: 準備 GitHub Repository
1. 創建新的 GitHub repository 或使用現有 repository
2. 將所有檔案上傳到 repository
3. 確保 repository 名稱為 `website`（或根據需要調整）

### 步驟 2: 配置 GitHub Pages
1. 進入 repository 的 **Settings** 頁面
2. 左側選擇 **Pages**
3. 在 **Source** 部分選擇：
   - Branch: `main` (或 `master`)
   - Folder: `/` (根目錄)
4. 點擊 **Save**

### 步驟 3: 等待部署完成
1. GitHub 會自動部署網站
2. 網站 URL 將為：`https://[你的用戶名].github.io/website/`
3. 部署通常需要 1-2 分鐘

## 功能測試

### 本地測試
1. 使用本地 HTTP 伺服器測試：
   ```bash
   # Python 3
   python -m http.server 8000
   
   # 或使用 Node.js http-server
   npx http-server
   ```
2. 訪問 `http://localhost:8000`
3. 測試登入和頁面導航功能

### 線上測試
1. 訪問 GitHub Pages URL
2. 測試以下功能：
   - [ ] 登入頁面顯示正常
   - [ ] 輸入任意帳號密碼（至少3字符）可登入
   - [ ] 登入後顯示導航欄
   - [ ] 點擊「儀表板」載入 `_8/code.html`
   - [ ] 點擊「趨勢」載入 `_2/code.html`
   - [ ] 點擊「行銷」載入 `_1/code.html`
   - [ ] 點擊「批次」載入 `_3/code.html`
   - [ ] 點擊「登出」返回登入頁面

## 技術細節

### Hash 路由系統
- 登入後 URL 會變為：`https://[用戶名].github.io/website/#dashboard`
- 支援的 hash 路由：
  - `#dashboard` - 儀表板
  - `#trends` - 趨勢報告
  - `#marketing` - 精準行銷
  - `#batch` - 批次導入
  - `#analyzer` - 分析儀原始版
  - `#analyzer-advanced` - 分析儀進階版

### 會話管理
- 登入狀態儲存在 `sessionStorage` 中
- 鍵值：
  - `忠茶_loggedIn`: `'true'` 或 `undefined`
  - `忠茶_userEmail`: 用戶電子郵件
- 瀏覽器關閉後會話自動清除

### 動態內容載入
1. 使用 `fetch()` 載入目標 HTML 檔案
2. 解析並提取主要內容
3. 修復相對路徑以適應 GitHub Pages
4. 執行內聯 JavaScript（有限制）

## 故障排除

### 常見問題

#### 問題 1: 頁面載入失敗
**症狀**：點擊導航按鈕後顯示「載入頁面失敗」
**解決方案**：
1. 檢查瀏覽器控制台錯誤
2. 確認目標 HTML 檔案存在
3. 檢查 GitHub Pages URL 結構
4. 修改 `index.html` 中的 `getBasePath()` 函數

#### 問題 2: CSS/JS 資源載入失敗
**症狀**：頁面樣式異常或功能失效
**解決方案**：
1. 檢查資源路徑是否正確
2. 在 `loadPage()` 函數中調整路徑修復邏輯
3. 確保 CDN 資源（Tailwind CSS, Google Fonts）可訪問

#### 問題 3: 登入後無法導航
**症狀**：登入成功但點擊導航無反應
**解決方案**：
1. 檢查 `sessionStorage` 是否正確設置
2. 檢查 JavaScript 控制台錯誤
3. 確認 `pageRoutes` 配置正確

#### 問題 4: GitHub Pages 顯示 404
**症狀**：訪問頁面顯示 404 錯誤
**解決方案**：
1. 確保 `404.html` 檔案存在
2. 檢查 GitHub Pages 設置
3. 等待部署完成（可能需要幾分鐘）

### 瀏覽器開發者工具
1. 按 **F12** 打開開發者工具
2. 檢查 **Console** 標籤頁的錯誤訊息
3. 檢查 **Network** 標籤頁的資源載入狀態
4. 檢查 **Application** → **Storage** → **Session Storage** 的登入狀態

## 自定義配置

### 修改頁面路由
編輯 `index.html` 中的 `pageRoutes` 對象：
```javascript
const pageRoutes = {
    'dashboard': './_8/code.html',
    'trends': './_2/code.html',
    // 添加或修改路由
};
```

### 調整基礎路徑
如果 GitHub Pages URL 不同，修改 `getBasePath()` 函數：
```javascript
function getBasePath() {
    const path = window.location.pathname;
    // 根據實際 repository 名稱調整
    if (path.includes('/repository-name/')) {
        return '/repository-name/';
    }
    return './';
}
```

### 添加新頁面
1. 創建新的 HTML 檔案
2. 添加到 `pageRoutes` 配置
3. 在導航欄添加對應按鈕（編輯 `index.html` 中的導航欄 HTML）

## 備註

### 安全性注意
- 此為純前端演示系統，無後端驗證
- 實際部署應添加後端 API 和身份驗證
- 敏感資料不應儲存在前端

### 性能優化
- 考慮預載入常用頁面
- 可添加載入動畫改善使用者體驗
- 對於大型頁面，考慮代碼分割

### 瀏覽器兼容性
- 支援現代瀏覽器（Chrome 60+, Firefox 55+, Safari 11+）
- 使用 ES6+ 特性，可能需要轉譯以支援舊瀏覽器

## 聯絡與支援

如有部署問題，請檢查：
1. GitHub Pages 文檔：https://docs.github.com/pages
2. 檢查 repository 的 **Actions** 標籤頁查看部署日誌
3. 確保所有檔案已正確提交

---

**最後更新**：2026-03-06  
**版本**：v2.0.0 (GitHub Pages 兼容版)