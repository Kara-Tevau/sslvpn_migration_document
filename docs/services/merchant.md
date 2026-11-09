# 商戶管理平台

Base：`http://192.168.13.89:80`

企業合作夥伴開卡、訂單、結算與 Webhook。`/console/**` 給內部營運；`/gate/**` 給已簽約租戶。

## 鑑權

### 營運臺 `/console`

驗證碼 + 帳密，可疊 TOTP。登入後發 OAuth2 風格 token（隨機串，BCrypt 存密碼）：

```http
POST /console/boot/captcha
POST /console/boot/enter
Authorization: <token>
```

`POST /console/boot/enter` Body：`username`、`password`、`captcha`、可選 `otp`。

### 租戶閘道 `/gate`

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

以 `appSecret` 做 HMAC-SHA256，輸出小寫十六進位。`type=1` 沙箱 KEY、`type=2` 生產 KEY，見 `/console/secret-pair/{type}`。

```sh
curl -sS 'http://192.168.13.89:80/console/boot/enter' \
  -H 'Content-Type: application/json' \
  -d '{"username":"YOUR_USERNAME","password":"YOUR_PASSWORD","captcha":"XXXX"}'
```

## 營運登入與驗證器

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| POST | `/console/boot/captcha` | 驗證碼 |
| POST | `/console/boot/enter` | 營運帳號登入 |
| POST | `/console/boot/enter-pass` | 密碼登入 |
| POST | `/console/boot/account` | 登入帳戶 |
| POST | `/console/boot/account-full` | 登入帳戶詳情 |
| POST | `/console/boot/change-pass` | 修改密碼（需驗證碼） |
| POST | `/console/boot/set-pass` | 設定新密碼 |
| POST | `/console/boot/set-mail` | 修改郵箱 |
| POST | `/console/boot/leave` | 退出 |
| POST | `/console/boot/company-enter` | 企業帳戶登入 |
| POST | `/console/boot/register` | 註冊 |
| POST | `/console/boot/routes` | 取得非同步路由 |
| POST | `/console/boot/middle` | 中間層登入 |
| POST | `/console/boot/sync-token` | 同步 token |
| POST | `/console/boot/drop-token` | 註銷 token |
| GET | `/console/otp/make` | 產生驗證器 |
| POST | `/console/otp/bind` | 綁定驗證器 |
| POST | `/console/otp/toggle` | 開關驗證器 |
| POST | `/console/otp/replace` | 更換驗證器 |
| GET | `/console/otp/flag` | 驗證器狀態 |

## 營運臺 `/console`

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| GET | `/console/staff/page` | 營運使用者分頁 |
| GET | `/console/staff/{id}` | 營運使用者資訊 |
| GET | `/console/staff/me` | 目前登入者 |
| PUT | `/console/staff/secret` | 修改密碼 |
| POST | `/console/staff` | 保存營運使用者 |
| PUT | `/console/staff` | 修改營運使用者 |
| DELETE | `/console/staff` | 刪除營運使用者 |
| GET | `/console/staff/export` | 匯出 |
| GET | `/console/staff/wipe-otp/{userId}` | 清除驗證器 |
| GET | `/console/acl/page` | 角色分頁 |
| GET | `/console/acl/list` | 角色列表 |
| POST | `/console/acl` | 保存角色 |
| PUT | `/console/acl` | 修改角色 |
| DELETE | `/console/acl` | 刪除角色 |
| GET | `/console/nav/tree` | 導航 |
| GET | `/console/nav/list` | 選單列表 |
| POST | `/console/nav` | 保存選單 |
| PUT | `/console/nav` | 修改選單 |
| DELETE | `/console/nav/{id}` | 刪除選單 |
| GET | `/console/org/list` | 部門列表 |
| POST | `/console/org` | 保存部門 |
| PUT | `/console/org` | 修改部門 |
| DELETE | `/console/org/{id}` | 刪除部門 |
| GET | `/console/knob/page` | 參數分頁 |
| POST | `/console/knob` | 保存參數 |
| PUT | `/console/knob` | 修改參數 |
| DELETE | `/console/knob` | 刪除參數 |
| GET | `/console/knob/export` | 匯出參數 |
| POST | `/console/firm/page` | 企業分頁 |
| POST | `/console/firm/update` | 修改企業 |
| POST | `/console/firm/save` | 新增企業 |
| POST | `/console/firm/open-tenant` | 終審完成後建立租戶 |
| DELETE | `/console/firm` | 刪除企業 |
| GET | `/console/firm/peek/{id}` | 企業詳情 |
| GET | `/console/firm/mine` | 目前使用者授權企業 |
| GET | `/console/firm/users` | 企業成員 |
| POST | `/console/intro/save` | 保存企業介紹並產生 KYB 租戶 |
| POST | `/console/intro/update` | 修改企業介紹 |
| POST | `/console/intro/upload` | 上傳介紹檔案 |
| DELETE | `/console/intro` | 刪除企業介紹 |
| GET | `/console/intro/peek/{id}` | 查詢企業介紹 |
| POST | `/console/intro/by-user` | 依使用者取企業介紹 |
| POST | `/console/rate-card/query` | 查詢企業費率 |
| POST | `/console/rate-card/update` | 修改企業費率 |
| POST | `/console/rate-card/save` | 新增企業費率 |
| POST | `/console/area/query` | 查詢國家地址 |
| POST | `/console/allow-ip/page` | IP 白名單分頁 |
| POST | `/console/allow-ip/update` | 修改 IP |
| POST | `/console/allow-ip/save` | 新增 IP |
| POST | `/console/allow-ip/delete` | 刪除 IP |
| POST | `/console/lexicon/query` | 查詢字典 |
| GET | `/console/secret-pair/{type}` | 查詢沙箱或生產 KEY。type=1 沙箱，type=2 生產 |
| POST | `/console/secret-pair/list` | KEY 分頁 |
| POST | `/console/kyb/user-page` | KYB 使用者分頁 |
| POST | `/console/kyb/audit-page` | KYB 審核分頁 |
| POST | `/console/kyb/by-user` | 使用者企業介紹 |
| POST | `/console/kyb/decide` | KYB 審核 |
| POST | `/console/kyb/tenant-page` | 租戶分頁 |
| POST | `/console/kyb/grant-page` | 權限關聯分頁 |
| POST | `/console/kyb/grant-save` | 新增權限關聯 |
| POST | `/console/kyb/grant-drop` | 刪除權限關聯 |
| POST | `/console/chain/page` | 鏈上地址分頁 |
| POST | `/console/chain/credit` | 企業充值 |
| POST | `/console/ledger/page` | 交易報表分頁 |
| GET | `/console/ledger/export` | 交易報表匯出 |
| POST | `/console/ledger/order-page` | 訂單列表分頁 |
| GET | `/console/ledger/order-export` | 訂單匯出 |
| POST | `/console/endpoint/page` | 對外 API 清單分頁 |
| POST | `/console/endpoint/update` | 修改 API 項 |
| POST | `/console/endpoint/save` | 新增 API 項 |
| DELETE | `/console/endpoint` | 刪除 API 項 |
| POST | `/console/busi-knob/page` | 業務配置分頁 |
| POST | `/console/busi-knob/add` | 新增業務配置 |
| POST | `/console/busi-knob/edit` | 編輯業務配置 |
| POST | `/console/kyc-queue/page` | KYC 審核分頁 |
| GET | `/console/kyc-queue/peek/{id}` | KYC 詳情 |
| POST | `/console/kyc-queue/decide` | KYC 審核 |
| GET | `/console/kyc-rule/list` | KYC 配置列表 |
| POST | `/console/kyc-rule/update` | 修改 KYC 配置 |
| POST | `/console/kyc-rule/save` | 新增 KYC 配置 |
| POST | `/console/member/page` | 下游客戶分頁 |
| GET | `/console/member/peek/{id}` | 下游客戶詳情 |
| POST | `/console/member/status` | 修改客戶狀態 |
| POST | `/console/auth-hook/page` | 授權回調分頁 |
| POST | `/console/warehouse/plastic/page` | 實體卡分頁 |
| POST | `/console/warehouse/serial/page` | 序號分頁 |
| GET | `/console/warehouse/serial/peek/{serialNumber}` | 依序號查詳情 |
| POST | `/console/warehouse/parcel/page` | 物流分頁 |
| GET | `/console/warehouse/parcel/peek/{id}` | 物流詳情 |
| POST | `/console/warehouse/parcel/confirm` | 確認卡號 |
| POST | `/console/warehouse/parcel/ship` | 發貨 |
| POST | `/console/warehouse/parcel/patch` | 更新物流 |
| POST | `/console/warehouse/stock/page` | 庫存分頁 |
| POST | `/console/warehouse/stock/save` | 保存庫存 |
| GET | `/console/warehouse/stock/by-title/{cardTitle}` | 依卡名查庫存 |
| POST | `/console/warehouse/stock/move` | 庫存調撥 |
| POST | `/console/mold/page` | 卡模板分頁 |
| GET | `/console/mold/grouped` | 依渠道與卡名分組 |
| GET | `/console/mold/titles` | 卡名列表 |
| POST | `/console/schedule-fee/page` | 月費年費紀錄 |
| GET | `/console/schedule-fee/export` | 月費年費匯出 |
| POST | `/console/batch-out/page` | 批量出卡分頁 |
| POST | `/console/batch-out/add` | 建立批量出卡 |

## 租戶閘道 `/gate`

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| POST | `/gate/media/issue/v2` | 提交開卡 |
| POST | `/gate/media/credit/v2` | 卡充值 |
| POST | `/gate/media/retire/v2` | 銷卡 |
| POST | `/gate/media/frost/v2` | 凍結卡 |
| POST | `/gate/media/thaw/v2` | 解凍卡 |
| POST | `/gate/media/latch/v2` | 鎖卡 |
| POST | `/gate/media/unlatch/v2` | 解鎖卡 |
| POST | `/gate/media/pan-html/v2` | 取得 PAN HTML |
| POST | `/gate/media/challenge/v2` | 3DS 驗證回應 |
| POST | `/gate/media/phone/v2` | 更新手機號 |
| POST | `/gate/media/mail/v2` | 更新郵箱 |
| POST | `/gate/media/fee/v2` | 更新卡費用 |
| POST | `/gate/media/activate/v2` | 激活卡 |
| POST | `/gate/media/attach/v2` | 綁卡 |
| POST | `/gate/lookup/wallet/v2` | 查企業錢包餘額 |
| POST | `/gate/lookup/book/v2` | 帳單列表 |
| POST | `/gate/lookup/book-one/v2` | 帳單詳情 |
| POST | `/gate/lookup/media-list/v2` | 客戶卡列表 |
| POST | `/gate/lookup/media-one/v2` | 客戶卡詳情 |
| POST | `/gate/lookup/pin/v2` | 卡 PIN 詳情 |
| POST | `/gate/lookup/caps/v2` | 卡限額 |
| POST | `/gate/lookup/fees/v2` | 卡交易手續費 |
| POST | `/gate/lookup/parcel/v2` | 物流查詢 |
| POST | `/gate/member/create/v2` | 建立企業下游使用者 |
| POST | `/gate/member/peek/v2` | 查下游使用者 |
| POST | `/gate/verify/submit/v2` | 提交 KYC |
| POST | `/gate/verify/peek/v2` | 取得 KYC 資訊 |
| POST | `/gate/verify/link/v2` | 取得 KYC 連結 |
| POST | `/gate/verify/level2/v2` | KYC 二級結果 |
| POST | `/gate/simulate/auth/v2` | 模擬授權 |
| POST | `/gate/simulate/clear/v2` | 模擬清算 |
| POST | `/gate/simulate/giveback/v2` | 模擬退款 |
| POST | `/gate/simulate/reverse/v2` | 模擬沖正 |
| POST | `/gate/simulate/challenge/v2` | 模擬 3DS 授權 |
| POST | `/gate/hook/register` | 註冊 Webhook |
| GET | `/gate/hook/list` | 查詢 Webhook |
| GET | `/gate/hook/drop` | 刪除 Webhook |
