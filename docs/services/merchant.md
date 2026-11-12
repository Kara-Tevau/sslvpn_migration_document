# 商戶管理平台

Base：`http://192.168.13.89:80`

企業合作夥伴開卡、訂單、結算與 Webhook。營運臺給內部帳號；租戶閘道給已簽約租戶。

## 鑑權

### 營運臺

驗證碼 + 帳密，可疊 TOTP。登入後發 OAuth2 風格 token（隨機串，BCrypt 存密碼）。後續請求：

```http
Authorization: <token>
```

登入 Body：`username`、`password`、`captcha`，可選 `otp`。

### 租戶閘道

租戶持有 `appKey` + `appSecret`。每個請求帶：

| 參數 | 位置 | 說明 |
| --- | --- | --- |
| `appKey` | Header `X-App-Key` | 沙箱或生產 |
| `ts` | Header `X-Ts` | Unix 秒 |
| `n` | Header `X-N` | 一次性字串 |
| `sign` | Header `X-Sign` | 簽名 |

`ts` 與伺服器相差超過 **180 秒** 拒絕。`n` 在視窗內不可重放。網關用 `appKey` 解析租戶編號後往下傳。IP 須在該租戶白名單內。

簽名字串（UTF-8）：

```text
appKey={appKey}&n={n}&ts={ts}&body={rawJsonBody}
```

以 `appSecret` 做 HMAC-SHA256，輸出小寫十六進位。`type=1` 沙箱 KEY、`type=2` 生產 KEY，在營運臺查詢，不回傳 secret 明文。

## 涵蓋

- 營運登入與驗證器：驗證碼、登入、改密、改郵箱、退出、企業登入、註冊、路由、token 同步與註銷、驗證器綁定與開關
- 營運臺：營運使用者、角色、選單、部門、參數、企業與介紹、費率、地址、IP 白名單、字典、沙箱／生產 KEY、KYB、鏈上地址、交易與訂單報表、API 清單、業務配置、KYC、下游客戶、授權回調、實體卡與物流庫存、卡模板、月費年費、批量出卡
- 租戶閘道：開卡、充值、銷卡、凍結與解凍、鎖卡與解鎖、PAN、3DS、聯絡方式、費用、激活、綁卡、錢包與帳單查詢、卡列表與 PIN、限額、物流、下游使用者、KYC、模擬授權／清算／退款／沖正、Webhook
