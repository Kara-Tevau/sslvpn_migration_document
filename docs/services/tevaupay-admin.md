# TevauPay 管理後台

Base：`http://192.168.13.3:80`

商務營運後台。瀏覽器開根路徑即登入頁。閘道帳號不能代替本後台帳號。

分頁類多數為 POST Body：`page` 從 1 起算、`pageSize` 預設 20。匯出為 GET。

## 鑑權

瀏覽器與腳本都用表單登入，欄位 `username`、`password`，`Content-Type` 為 `application/x-www-form-urlencoded`，`Accept` 為 `application/json`。

成功：`200`，前端進入工作臺。  
失敗：`401`，JSON 含 `error`、`retry_after_seconds`。同一來源需間隔至少 5 秒，否則 `429`。

登入後續請求帶工作階段 Cookie。JSON API 另帶隨機會話 token（**不是 JWT**，服務端 Redis 對應 loginId）：

```http
Authorization: <token>
```

密碼為加鹽雜湊。權限為角色 / 選單 / 部門資料範圍。可選 TOTP。寫入類操作會記操作日誌。

## 涵蓋

- 頁面：登入頁、登入後工作臺（未登入會轉回登入）
- 工作階段：登入、退出
- 系統設定：管理員、角色、選單、部門、字典、系統參數、文案、圖片庫、列舉、物件儲存、檔案匯出
- 業務臺：客戶、灰度、登入紀錄、第三方綁定、地址、認證紀錄、卡片、序號、卡模板、額度、庫存、凍結、開卡失敗、物流、充值與交易、退款、調帳、KYC、黑白名單
