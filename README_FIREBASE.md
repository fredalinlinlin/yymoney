# Firebase 設定指南 - 淵 & 妍 記帳本

本指南將引導你完成 Firebase 設定，讓記帳資料可以在多個裝置間同步。

## 為什麼需要 Firebase？

- **多裝置同步**：在手機、電腦間即時同步記帳資料
- **資料備份**：資料儲存在雲端，不怕裝置遺失
- **即時更新**：淵記帳後，妍的裝置立即看到（1-2 秒內）

## 設定步驟

### 步驟 1: 建立 Firebase 專案

1. 前往 [Firebase Console](https://console.firebase.google.com/)
2. 點擊「新增專案」
3. 輸入專案名稱（例如：`yymoney-app`）
4. 停用 Google Analytics（非必要）
5. 點擊「建立專案」

### 步驟 2: 啟用 Realtime Database

1. 在 Firebase Console 左側選單，點擊「Realtime Database」
2. 點擊「建立資料庫」
3. 選擇資料庫位置（建議選擇亞洲，例如 `asia-southeast1`）
4. 選擇「鎖定模式」（稍後會設定 Security Rules）
5. 點擊「啟用」

### 步驟 3: 設定 Security Rules（重要！）

1. 在 Realtime Database 頁面，點擊「規則」分頁
2. 將規則替換為以下內容：

```json
{
  "rules": {
    "expenses": {
      ".read": "auth != null && (auth.token.email == '淵的email@example.com' || auth.token.email == '妍的email@example.com')",
      ".write": "auth != null && (auth.token.email == '淵的email@example.com' || auth.token.email == '妍的email@example.com')"
    }
  }
}
```

**注意**：請將 `淵的email@example.com` 和 `妍的email@example.com` 替換為你們實際要使用的 Email 地址。

3. 點擊「發布」

### 步驟 4: 啟用 Email/Password 認證

1. 在 Firebase Console 左側選單，點擊「Authentication」
2. 點擊「開始使用」
3. 在「登入方式」分頁，啟用「電子郵件/密碼」
4. 點擊「儲存」

### 步驟 5: 建立使用者帳號

1. 在 Authentication 頁面，點擊「Users」分頁
2. 點擊「新增使用者」
3. 建立第一個帳號：
   - Email: `淵的email@example.com`（需與 Security Rules 一致）
   - Password: 自訂密碼（至少 6 個字元）
4. 再次點擊「新增使用者」建立第二個帳號：
   - Email: `妍的email@example.com`
   - Password: 自訂密碼

**重要**：請記住這兩組 Email 和密碼，稍後需要用來登入應用程式。

### 步驟 6: 取得 Firebase 配置

1. 在 Firebase Console 左側選單，點擊「專案設定」（齒輪圖示）
2. 捲動到「你的應用程式」區域
3. 點擊「Web」圖示（`</>`）
4. 輸入應用程式暱稱（例如：`yymoney-web`）
5. **不要**勾選「同時設定 Firebase Hosting」
6. 點擊「註冊應用程式」
7. 複製 `firebaseConfig` 物件中的所有值：

```javascript
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "yymoney-app.firebaseapp.com",
  databaseURL: "https://yymoney-app-default-rtdb.firebaseio.com",
  projectId: "yymoney-app",
  storageBucket: "yymoney-app.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

### 步驟 7: 在應用程式中設定 Firebase

1. 開啟記帳本應用程式（`index.html`）
2. 切換到「⚙️ 設定」分頁
3. 在「Firebase 配置」區域，依序輸入步驟 6 複製的值：
   - API Key
   - Auth Domain
   - Database URL
   - Project ID
   - Storage Bucket
   - Messaging Sender ID
   - App ID
4. 點擊「儲存配置」
5. 在「登入」區域輸入你的 Email 和密碼
6. 點擊「登入」
7. 看到「🟢 已同步」表示成功！

### 步驟 8: 首次資料同步（如果有舊資料）

如果你之前已經有記帳資料儲存在本地：

1. 登入成功後，在「設定」頁面找到「資料同步」區域
2. 點擊「上傳本地資料到雲端」按鈕
3. 確認資料已同步到 Firebase

## 在其他裝置上設定

1. 在新裝置上開啟記帳本應用程式
2. 重複**步驟 7**（輸入 Firebase 配置和登入）
3. 登入後，雲端資料會自動下載到新裝置

## 常見問題

### Q: Firebase 配置會被提交到 GitHub 嗎？

不會。配置儲存在你的瀏覽器本地儲存空間（`window.storage`），不會出現在 GitHub 上。

### Q: 其他人可以存取我的資料嗎？

不行。Security Rules 限制只有你們兩個 Email 可以讀寫資料。即使別人知道 Firebase 配置，也無法存取。

### Q: 如果兩人同時記帳會怎樣？

不會有問題！Firebase 使用物件結構儲存每筆記錄，可以自動合併變更，不會遺失資料。

### Q: 離線時可以記帳嗎？

可以！資料會先儲存在本地，等網路恢復後自動同步到雲端。

### Q: Firebase 免費嗎？

是的！Realtime Database 免費額度為 1GB 儲存空間和 10GB/月下載量，對記帳應用來說綽綽有餘。

### Q: 忘記密碼怎麼辦？

在 Firebase Console 的 Authentication > Users 頁面，可以重設使用者密碼。

### Q: 可以備份資料嗎？

可以！在「設定」頁面點擊「匯出資料」，會下載 JSON 格式的備份檔案。

## 安全建議

1. **不要分享密碼**：即使是淵和妍，也應該各自使用自己的帳號
2. **定期備份**：建議每月匯出一次資料備份
3. **檢查 Security Rules**：確保 Email 白名單正確設定
4. **監控用量**：在 Firebase Console 檢查資料庫用量，避免超出免費額度

## 移除 Firebase 同步

如果想停用雲端同步，回到純本地模式：

1. 在「設定」頁面點擊「登出」
2. 點擊「清除 Firebase 配置」
3. 應用程式會回到本地儲存模式

## 需要協助？

如果遇到問題，請檢查：

1. Firebase Console 的「使用情況」分頁，確認有資料活動
2. 瀏覽器開發者工具（F12）的 Console，查看錯誤訊息
3. Security Rules 的 Email 是否與登入帳號一致

---

設定完成後，就可以在任何裝置上同步記帳資料了！ 🎉
