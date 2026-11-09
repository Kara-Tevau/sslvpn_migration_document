# 統一身分管理

Base：`http://192.168.13.47:80`

公司目錄、SSO 與應用授權。閘道只解決能不能進網段；各後台 Token 仍由本系統核發。

目錄查詢可走 GET Query；寫入走 JSON Body。

## 鑑權

```http
POST /iam/session/open
Content-Type: application/json

{
  "username": "YOUR_USERNAME",
  "password": "YOUR_PASSWORD",
  "otp": ""
}
```

成功回 `accessToken`、`expiresIn`、`tokenType=Bearer`。已開 OTP 必填 `otp`。後續：

```http
Authorization: Bearer <accessToken>
```

會話 token 為隨機字串，對應關係在 Redis，**本地無法解析**。登入方式含目錄密碼、第三方 IdP 與 TOTP。不要把閘道 Cookie 當成身分 Token。

```sh
curl -sS 'http://192.168.13.47:80/iam/session/open' \
  -H 'Content-Type: application/json' \
  -d '{"username":"YOUR_USERNAME","password":"YOUR_PASSWORD"}'
```

## 工作階段與 OTP

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| POST | `/iam/session/open` | 建立工作階段。Body：username、password、otp |
| GET | `/iam/session/me` | 目前工作階段 |
| POST | `/iam/session/close` | 註銷 Token |
| PUT | `/iam/session/secret` | 修改密碼。Body：oldPassword、newPassword |
| POST | `/iam/otp/check` | 驗證 OTP。Body：code |
| POST | `/iam/otp/enroll` | 綁定驗證器 |
| POST | `/iam/otp/drop` | 解除驗證器 |
| GET | `/iam/otp/status` | 驗證器狀態 |

## 目錄、部門、角色、選單

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| GET | `/iam/people/page` | 使用者分頁。Query：page、pageSize、q、orgId |
| GET | `/iam/people/{id}` | 使用者詳情 |
| POST | `/iam/people` | 建立使用者 |
| PUT | `/iam/people/{id}` | 更新使用者 |
| POST | `/iam/people/{id}/freeze` | 停用使用者 |
| POST | `/iam/people/{id}/thaw` | 啟用使用者 |
| GET | `/iam/people/export` | 匯出使用者 |
| GET | `/iam/people/{id}/wipe-otp` | 清除驗證器 |
| GET | `/iam/org/list` | 部門樹 |
| GET | `/iam/org/{id}` | 部門資訊 |
| POST | `/iam/org` | 保存部門 |
| PUT | `/iam/org` | 修改部門 |
| DELETE | `/iam/org/{id}` | 刪除部門 |
| GET | `/iam/org/{id}/members` | 部門成員 |
| GET | `/iam/seat/page` | 角色分頁 |
| GET | `/iam/seat/list` | 角色列表 |
| GET | `/iam/seat/{id}` | 角色資訊 |
| POST | `/iam/seat` | 保存角色 |
| PUT | `/iam/seat` | 修改角色 |
| DELETE | `/iam/seat` | 刪除角色 |
| GET | `/iam/panel/tree` | 導航樹 |
| GET | `/iam/panel/flags` | 權限標識 |
| GET | `/iam/panel/list` | 選單列表 |
| GET | `/iam/panel/{id}` | 選單資訊 |
| POST | `/iam/panel` | 保存選單 |
| PUT | `/iam/panel` | 修改選單 |
| DELETE | `/iam/panel/{id}` | 刪除選單 |
| GET | `/iam/panel/for-seat` | 角色選單權限 |
| GET | `/iam/bundle/page` | 使用者組分頁 |
| GET | `/iam/bundle/list` | 使用者組列表 |
| GET | `/iam/bundle/{id}` | 使用者組資訊 |
| POST | `/iam/bundle` | 保存使用者組 |
| PUT | `/iam/bundle` | 修改使用者組 |
| DELETE | `/iam/bundle` | 刪除使用者組 |
| GET | `/iam/bundle-user/page` | 組內成員分頁 |
| GET | `/iam/bundle-user/list` | 組內成員列表 |
| POST | `/iam/bundle-user` | 加入成員 |
| DELETE | `/iam/bundle-user` | 移出成員 |

## SSO / OAuth

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| GET | `/iam/saml/descriptor` | SAML Metadata |
| POST | `/iam/saml/acs` | SAML ACS |
| GET | `/iam/oidc/authorize` | 授權碼。Query：client_id、redirect_uri、response_type、state |
| POST | `/iam/oidc/token` | 換 Token |
| GET | `/iam/oidc/userinfo` | OIDC userinfo |
| POST | `/iam/oidc/revoke` | 撤銷 Token |
| GET | `/iam/clients/page` | 接入應用分頁 |
| GET | `/iam/clients/{id}` | 應用詳情 |
| POST | `/iam/clients` | 新增應用 |
| PUT | `/iam/clients` | 修改應用 |
| DELETE | `/iam/clients/{id}` | 刪除應用 |
| GET | `/iam/clients/{id}/secret-hint` | 密鑰提示（不回傳明文） |
| POST | `/iam/clients/{id}/rotate` | 輪換應用密鑰 |

## 第三方登入

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| POST | `/iam/ext/idp/start` | 第三方登入起始 |
| POST | `/iam/ext/idp/finish` | 第三方登入完成 |
| POST | `/iam/ext/idp/device-finish` | 裝置內建帳號登入 |
| POST | `/iam/ext/idp/push-finish` | 推播身分登入 |
| GET | `/iam/ext/bind/page` | 第三方綁定分頁 |
| GET | `/iam/ext/bind/peek` | 第三方綁定詳情 |
| POST | `/iam/ext/bind/detach` | 解綁 |
| POST | `/iam/ext/bind/log-page` | 第三方登入日誌 |

## 稽核與輔助

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| GET | `/iam/trail/signin/page` | 登入日誌分頁 |
| GET | `/iam/trail/signin/export` | 登入日誌匯出 |
| GET | `/iam/trail/error/page` | 異常日誌分頁 |
| GET | `/iam/trail/error/export` | 異常日誌匯出 |
| GET | `/iam/trail/action/page` | 操作日誌分頁 |
| GET | `/iam/trail/action/export` | 操作日誌匯出 |
| GET | `/iam/region/list/{lan}` | 國家地區列表 |
| GET | `/iam/device/page` | 裝置列表 |
| POST | `/iam/device/kick` | 強制下線 |
| POST | `/iam/mail/exists` | 郵箱是否已註冊 |
| POST | `/iam/mail/login-hint` | 依帳號取登入郵箱 |
| GET | `/iam/net/where` | 目前 IP 與地區 |
| POST | `/iam/lang/switch` | 切換語言 |
| POST | `/iam/nick/update` | 更新顯示名 |
| POST | `/iam/phone/set` | 設定手機號 |
| GET | `/iam/phone/info` | 取得手機號資訊 |
| POST | `/iam/pay-pin/bind` | 設定支付密碼 |
| POST | `/iam/pay-pin/forget` | 忘記支付密碼 |
| POST | `/iam/pay-pin/update` | 修改支付密碼 |
| POST | `/iam/register` | 註冊目錄帳號 |
| POST | `/iam/recover/secret` | 重設密碼 |
| GET | `/iam/token/parse-ext` | 解析三方 token |
| POST | `/iam/lookup/by-mail` | 依郵箱或邀請碼查使用者 |
| POST | `/iam/invite/issue` | 發放邀請碼 |
| POST | `/iam/invite/consume` | 使用邀請碼 |
| GET | `/iam/invite/page` | 邀請碼分頁 |
| POST | `/iam/svc-key/page` | 服務帳號金鑰分頁 |
| POST | `/iam/svc-key/create` | 建立服務帳號金鑰 |
| POST | `/iam/svc-key/revoke/{id}` | 撤銷服務帳號金鑰 |
| GET | `/iam/policy/page` | 資料範圍策略分頁 |
| POST | `/iam/policy/save` | 保存資料範圍策略 |
| PUT | `/iam/policy` | 修改資料範圍策略 |
| DELETE | `/iam/policy/{id}` | 刪除資料範圍策略 |
| GET | `/iam/session/list` | 目前使用者的有效會話 |
| POST | `/iam/session/kick-others` | 踢掉其他會話 |
