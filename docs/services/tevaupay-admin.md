# TevauPay 管理後台

Base：`http://192.168.13.3:80`

商務營運後台。瀏覽器開根路徑即登入頁。閘道帳號不能代替本後台帳號。

分頁類多數為 POST Body：`page` 從 1 起算、`pageSize` 預設 20。詳情習慣用 `peek/{id}`。匯出為 GET。

## 鑑權

瀏覽器登入（也是腳本應走的入口）：

```http
POST /api/login
Content-Type: application/x-www-form-urlencoded
Accept: application/json

username={帳號}&password={密碼}
```

成功：`200`，前端進 `/dashboard`。  
失敗：`401`，JSON 含 `error`、`retry_after_seconds`。同一來源需間隔至少 5 秒，否則 `429`。

登入後續請求帶工作階段 Cookie。JSON API 另帶隨機會話 token（**不是 JWT**，服務端 Redis 對應 loginId）：

```http
Authorization: <token>
```

密碼為加鹽雜湊。權限為角色 / 選單 / 部門資料範圍。可選 TOTP。寫入類操作會記操作日誌。

```sh
curl -sS -X POST 'http://192.168.13.3:80/api/login' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-urlencode 'username=YOUR_USERNAME' \
  --data-urlencode 'password=YOUR_PASSWORD'
```

## 頁面

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| GET | `/` | 登入頁 |
| GET | `/login` | 登入頁 |
| GET | `/dashboard` | 登入後工作臺；未登入轉回 /login |

## 工作階段

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| POST | `/api/login` | 後台登入。表單欄位 username、password |
| POST | `/api/logout` | 退出 |

## 系統設定 `/adm`

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| GET | `/adm/profile/me` | 登入者資訊 |
| PUT | `/adm/profile/secret` | 修改本人密碼 |
| GET | `/adm/staff/page` | 管理員分頁 |
| GET | `/adm/staff/{id}` | 管理員資訊 |
| POST | `/adm/staff` | 保存管理員 |
| PUT | `/adm/staff` | 修改管理員 |
| DELETE | `/adm/staff` | 刪除管理員 |
| GET | `/adm/staff/export` | 匯出管理員 |
| GET | `/adm/acl/page` | 角色分頁 |
| GET | `/adm/acl/list` | 角色列表 |
| GET | `/adm/acl/{id}` | 角色資訊 |
| POST | `/adm/acl` | 保存角色 |
| PUT | `/adm/acl` | 修改角色 |
| DELETE | `/adm/acl` | 刪除角色 |
| GET | `/adm/nav/tree` | 側邊欄導航 |
| GET | `/adm/nav/flags` | 權限標識 |
| GET | `/adm/nav/list` | 選單列表 |
| GET | `/adm/nav/{id}` | 選單資訊 |
| POST | `/adm/nav` | 保存選單 |
| PUT | `/adm/nav` | 修改選單 |
| DELETE | `/adm/nav/{id}` | 刪除選單 |
| GET | `/adm/nav/for-acl` | 角色選單權限 |
| GET | `/adm/org/list` | 部門列表 |
| GET | `/adm/org/{id}` | 部門資訊 |
| POST | `/adm/org` | 保存部門 |
| PUT | `/adm/org` | 修改部門 |
| DELETE | `/adm/org/{id}` | 刪除部門 |
| GET | `/adm/catalog/kind/page` | 字典類型分頁 |
| GET | `/adm/catalog/kind/{id}` | 字典類型資訊 |
| POST | `/adm/catalog/kind` | 保存字典類型 |
| PUT | `/adm/catalog/kind` | 修改字典類型 |
| DELETE | `/adm/catalog/kind` | 刪除字典類型 |
| GET | `/adm/catalog/kind/all` | 全部字典資料 |
| GET | `/adm/catalog/item/page` | 字典資料分頁 |
| GET | `/adm/catalog/item/{id}` | 字典資料資訊 |
| POST | `/adm/catalog/item` | 保存字典資料 |
| PUT | `/adm/catalog/item` | 修改字典資料 |
| DELETE | `/adm/catalog/item` | 刪除字典資料 |
| GET | `/adm/knob/page` | 系統參數分頁 |
| GET | `/adm/knob/{id}` | 系統參數資訊 |
| POST | `/adm/knob` | 保存系統參數 |
| PUT | `/adm/knob` | 修改系統參數 |
| DELETE | `/adm/knob` | 刪除系統參數 |
| GET | `/adm/knob/export` | 匯出系統參數 |
| POST | `/adm/copy/page` | 文案分頁 |
| POST | `/adm/copy/save` | 保存文案 |
| POST | `/adm/copy/update` | 修改文案 |
| DELETE | `/adm/copy/drop/{configId}` | 刪除文案 |
| POST | `/adm/copy/reload` | 刷新文案快取 |
| GET | `/adm/copy/lock` | 鎖定文案快取 |
| GET | `/adm/copy/unlock` | 解鎖文案快取 |
| POST | `/adm/album/page` | 圖片庫分頁 |
| GET | `/adm/album/by-kind/{type}` | 依類型取圖片庫 |
| POST | `/adm/album/save` | 新增圖片 |
| POST | `/adm/album/update` | 修改圖片 |
| DELETE | `/adm/album/drop/{id}` | 刪除圖片 |
| GET | `/adm/choice/by-names` | 依列舉名稱查詢 |
| POST | `/adm/choice/by-names` | 依列舉名稱查詢 |
| GET | `/adm/choice/all` | 查詢全部列舉 |
| POST | `/adm/choice/all` | 查詢全部列舉 |
| GET | `/adm/blob/page` | 物件儲存分頁 |
| GET | `/adm/blob/info` | 物件儲存設定 |
| POST | `/adm/blob` | 保存物件儲存設定 |
| POST | `/adm/blob/upload` | 上傳檔案 |
| DELETE | `/adm/blob` | 刪除物件 |
| GET | `/adm/dump/file` | 通用檔案匯出 |

## 業務臺 `/desk`

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| POST | `/desk/holder/page` | 客戶分頁 |
| GET | `/desk/holder/peek/{id}` | 客戶詳情 |
| POST | `/desk/holder/invite-code` | 修改邀請碼 |
| POST | `/desk/holder/status` | 修改客戶狀態 |
| POST | `/desk/holder/invite-tier` | 修改邀請等級 |
| POST | `/desk/holder/close/{id}` | 註銷客戶 |
| POST | `/desk/holder/tag` | 修改標籤 |
| GET | `/desk/holder/wipe-totp/{userId}` | 清除驗證器資訊 |
| GET | `/desk/holder/code-options` | 客戶編號下拉 |
| POST | `/desk/holder/relink-upline` | 調整上級 |
| POST | `/desk/holder/tree` | 查詢上下級樹 |
| POST | `/desk/canary/page` | 灰度名單分頁 |
| POST | `/desk/canary/add` | 加入灰度名單 |
| POST | `/desk/canary/remove` | 移出灰度名單 |
| POST | `/desk/signin-log/page` | 登入紀錄分頁 |
| GET | `/desk/signin-log/export` | 登入紀錄匯出 |
| POST | `/desk/ext-bind/page` | 第三方帳號分頁 |
| GET | `/desk/ext-bind/peek` | 第三方帳號詳情 |
| GET | `/desk/ext-bind/detach` | 解綁第三方帳號 |
| POST | `/desk/addr/page` | 收貨地址分頁 |
| POST | `/desk/cert/page` | 三方認證紀錄分頁 |
| POST | `/desk/plastic/page` | 卡片分頁 |
| GET | `/desk/plastic/export` | 卡片匯出 |
| GET | `/desk/plastic/peek/{id}` | 卡片詳情 |
| POST | `/desk/plastic/status` | 修改卡狀態 |
| GET | `/desk/plastic/agent-template` | 下載代理導入模板 |
| POST | `/desk/plastic/agent-import` | 導入代理卡 |
| GET | `/desk/plastic/retire` | 註銷卡 |
| GET | `/desk/plastic/balance/{id}` | 取得卡餘額 |
| GET | `/desk/plastic/unlock/{id}` | 解鎖卡 |
| POST | `/desk/plastic/pin-reset` | 重設 PIN |
| GET | `/desk/plastic/tokens` | 取得 token 集合 |
| GET | `/desk/plastic/challenge-detail` | 取得 3DS 詳情 |
| GET | `/desk/plastic/prefs` | 取得卡設定 |
| GET | `/desk/plastic/prefs-save` | 更新卡設定 |
| GET | `/desk/plastic/retire-batch` | 批量註銷卡 |
| GET | `/desk/plastic/balance-script` | 腳本查餘額 |
| POST | `/desk/plastic/fx-table` | 查詢匯率 |
| POST | `/desk/plastic/token-status` | 修改 Token 狀態 |
| POST | `/desk/serial/page` | 實體卡序號分頁 |
| GET | `/desk/serial/template` | 下載序號模板 |
| POST | `/desk/serial/import` | 導入序號 |
| GET | `/desk/serial/by-no/{serialNumber}` | 依序號取卡號與顏色 |
| POST | `/desk/mold/page` | 卡模板分頁 |
| GET | `/desk/mold/peek/{id}` | 卡模板詳情 |
| POST | `/desk/mold/save` | 新增卡模板 |
| POST | `/desk/mold/update` | 修改卡模板 |
| GET | `/desk/mold/cascade` | 依卡方與卡頭取聯動資料 |
| GET | `/desk/mold/platforms` | 卡方下拉 |
| GET | `/desk/mold/titles` | 卡頭下拉 |
| GET | `/desk/mold/by-code/{cardCode}` | 依卡編號取模板 |
| GET | `/desk/mold/codes` | 卡編號下拉 |
| POST | `/desk/mold/reload` | 刷新模板快取 |
| POST | `/desk/cap/add` | 新增額度 |
| POST | `/desk/cap/update` | 修改額度 |
| POST | `/desk/cap/page` | 額度分頁 |
| POST | `/desk/stock/page` | 庫存分頁 |
| POST | `/desk/stock/save` | 新增庫存 |
| GET | `/desk/stock/by-title/{platformType}/{cardTitle}` | 依卡頭取庫存 |
| POST | `/desk/stock/push-out` | 庫存調出至發行行 |
| POST | `/desk/stock/push-out-query` | 庫存調出查詢 |
| POST | `/desk/stock/pull-in` | 發行行調回庫存 |
| POST | `/desk/stock/pull-in-query` | 發行行調回查詢 |
| POST | `/desk/frost/page` | 凍結歷史分頁 |
| POST | `/desk/fail-log/page` | 開卡失敗日誌分頁 |
| POST | `/desk/parcel/page` | 物流分頁 |
| GET | `/desk/parcel/peek/{id}` | 物流詳情 |
| POST | `/desk/parcel/patch` | 修改物流資訊 |
| POST | `/desk/parcel/status` | 修改物流狀態 |
| POST | `/desk/parcel/confirm` | 確認卡號 |
| GET | `/desk/parcel/export` | 物流匯出 |
| POST | `/desk/topup/page` | 卡充值紀錄分頁 |
| POST | `/desk/spend/page` | 卡交易紀錄分頁 |
| GET | `/desk/topup/export` | 充值匯出 |
| GET | `/desk/spend/export` | 交易匯出 |
| POST | `/desk/giveback/page` | 待退款分頁 |
| GET | `/desk/giveback/export` | 待退款匯出 |
| POST | `/desk/giveback/check-page` | 核查交易分頁 |
| POST | `/desk/giveback/same-amount` | 相同金額訂單核查 |
| GET | `/desk/giveback/status` | 修改退款狀態 |
| GET | `/desk/giveback/note` | 修改退款備註 |
| GET | `/desk/giveback/send` | 發起退款 |
| POST | `/desk/giveback/all` | 一鍵退款 |
| POST | `/desk/giveback/all-sum` | 一鍵退款金額統計 |
| POST | `/desk/shift/apply` | 調帳 |
| POST | `/desk/shift/page` | 調帳紀錄列表 |
| POST | `/desk/kyc-queue/page` | KYC 審核分頁 |
| GET | `/desk/kyc-queue/peek/{id}` | KYC 審核詳情 |
| POST | `/desk/kyc-queue/decide` | KYC 審核 |
| GET | `/desk/kyc-rule/list` | KYC 審核配置列表 |
| POST | `/desk/kyc-rule/update` | 修改 KYC 審核配置 |
| POST | `/desk/kyc-rule/save` | 新增 KYC 審核配置 |
| POST | `/desk/guard/page` | 黑白名單分頁 |
| POST | `/desk/guard/add` | 批量加入黑白名單 |
| POST | `/desk/guard/remove` | 批量移除黑白名單 |
| GET | `/desk/guard/reload` | 刷新黑白名單快取 |
