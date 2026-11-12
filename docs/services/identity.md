# 統一身分管理

Base：`http://192.168.13.47:80`

公司目錄、SSO 與應用授權。閘道只解決能不能進網段；各後台 Token 仍由本系統核發。

目錄查詢可走 GET Query；寫入走 JSON Body。

## 鑑權

JSON 登入。Body：`username`、`password`，已開 OTP 時另帶 `otp`。

成功回 `accessToken`、`expiresIn`、`tokenType=Bearer`。後續：

```http
Authorization: Bearer <accessToken>
```

會話 token 為隨機字串，對應關係在 Redis，**本地無法解析**。登入方式含目錄密碼、第三方 IdP 與 TOTP。不要把閘道 Cookie 當成身分 Token。

## 涵蓋

- 工作階段與 OTP：建立與註銷工作階段、改密碼、綁定／解除／驗證 OTP
- 目錄：使用者、部門、角色、選單、使用者組
- SSO / OAuth：SAML、授權碼、Token、接入應用與密鑰輪換
- 第三方登入：起始、完成、綁定與登入日誌
- 稽核與輔助：登入／異常／操作日誌、地區、裝置下線、郵箱、語言、顯示名、手機、支付密碼、註冊與重設、邀請碼、服務帳號金鑰、資料範圍
