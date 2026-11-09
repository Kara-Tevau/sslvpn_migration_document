# 營運管理平台

Base：`http://192.168.13.231:80`

活動、積分、公告、值班、工單、內容與排程。卡片資金處置請走 TevauPay 管理後台與財務系統。

## 鑑權

帳密換隨機 token。Header `Authorization: <token>`。可疊 TOTP。角色決定看得見的選單；工單另有分派範圍。

```http
POST /workbench/session/open
Content-Type: application/json

{
  "username": "YOUR_USERNAME",
  "password": "YOUR_PASSWORD"
}
```

```sh
curl -sS 'http://192.168.13.231:80/workbench/session/open' \
  -H 'Content-Type: application/json' \
  -d '{"username":"YOUR_USERNAME","password":"YOUR_PASSWORD"}'
```

## 工作階段與首頁

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| POST | `/workbench/session/open` | 登入。Body：username、password |
| POST | `/workbench/session/close` | 退出 |
| GET | `/workbench/session/me` | 目前登入者 |
| GET | `/workbench/home/snapshot` | 待辦、在線、未關工單 |

## 活動與券

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| GET | `/workbench/campaign/kinds` | 活動類型列表 |
| POST | `/workbench/campaign/list` | 活動列表（不分頁） |
| POST | `/workbench/campaign/save` | 新增活動 |
| POST | `/workbench/campaign/update` | 修改活動 |
| POST | `/workbench/campaign/term/page` | 期數分頁 |
| POST | `/workbench/campaign/term/save` | 新增期數 |
| POST | `/workbench/campaign/term/update` | 修改期數 |
| POST | `/workbench/campaign/term/drop` | 刪除期數 |
| POST | `/workbench/campaign/board/list` | 榜單列表 |
| POST | `/workbench/campaign/board/save` | 新增榜單 |
| POST | `/workbench/campaign/board/update` | 修改榜單 |
| POST | `/workbench/campaign/board/drop` | 刪除榜單 |
| POST | `/workbench/campaign/credit/once` | 活動帳戶單次充值 |
| POST | `/workbench/campaign/credit/page` | 活動充值日誌 |
| GET | `/workbench/campaign/credit/options` | 活動下拉 |
| POST | `/workbench/campaign/credit/by-campaign` | 依活動批量充值 |
| POST | `/workbench/campaign/block/list` | 活動黑名單 |
| POST | `/workbench/campaign/block/save` | 新增活動黑名單 |
| POST | `/workbench/campaign/block/update` | 修改活動黑名單 |
| POST | `/workbench/campaign/block/drop` | 刪除活動黑名單 |
| GET | `/workbench/campaign/fest/export` | 節慶活動資料匯出 |
| POST | `/workbench/campaign/fest/import-board` | 導入節慶榜單 |
| POST | `/workbench/campaign/fest/import-invite` | 導入邀請人數榜 |
| GET | `/workbench/campaign/fest/init-weight` | 初始化獎品權重 |
| POST | `/workbench/campaign/prize/lookup` | 發獎前核對 |
| POST | `/workbench/campaign/prize/send` | 單條發獎 |
| GET | `/workbench/campaign/prize/options` | 發獎活動下拉 |
| POST | `/workbench/campaign/prize/page` | 發獎日誌 |
| POST | `/workbench/coupon/page` | 代金券分頁 |
| POST | `/workbench/coupon/import` | 導入代金券使用者 |
| GET | `/workbench/coupon/export` | 代金券匯出 |
| POST | `/workbench/coupon/import-page` | 代金券導入分頁 |
| POST | `/workbench/coupon/send` | 發送代金券 |
| POST | `/workbench/promo-code/page` | 折扣碼分頁 |
| GET | `/workbench/promo-code/peek/{configId}` | 折扣碼詳情 |
| POST | `/workbench/promo-code/save` | 新增折扣碼 |
| POST | `/workbench/promo-code/update` | 修改折扣碼 |
| POST | `/workbench/promo-code/usage/page` | 折扣碼使用分頁 |
| GET | `/workbench/promo-code/usage/export` | 折扣碼使用匯出 |

## 積分與返佣

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| GET | `/workbench/credit/board/overview` | 積分看板 |
| POST | `/workbench/credit/prize/page` | 兌換配置分頁 |
| POST | `/workbench/credit/prize/save` | 新增兌換配置 |
| POST | `/workbench/credit/prize/update` | 修改兌換配置 |
| GET | `/workbench/credit/prize/info/{id}` | 兌換配置詳情 |
| GET | `/workbench/credit/box/info` | 高級盒配置 |
| POST | `/workbench/credit/box/update` | 修改高級盒配置 |
| GET | `/workbench/credit/guard/info` | 防刷配置 |
| POST | `/workbench/credit/guard/update` | 修改防刷配置 |
| POST | `/workbench/credit/code/page` | 積分碼分頁 |
| POST | `/workbench/credit/code/create` | 建立積分碼 |
| POST | `/workbench/credit/code/batch` | 批量建立積分碼 |
| POST | `/workbench/credit/code/disable/{id}` | 停用積分碼 |
| GET | `/workbench/credit/code/info/{id}` | 積分碼詳情 |
| POST | `/workbench/credit/code/usages/{codeId}` | 積分碼使用紀錄 |
| GET | `/workbench/credit/code/export` | 積分碼匯出 |
| POST | `/workbench/credit/usage/page` | 使用紀錄分頁 |
| GET | `/workbench/credit/usage/export` | 使用紀錄匯出 |
| POST | `/workbench/credit/band/page` | 金額區間分頁 |
| POST | `/workbench/credit/band/save` | 新增金額區間 |
| POST | `/workbench/credit/band/update` | 修改金額區間 |
| POST | `/workbench/credit/band/drop/{id}` | 刪除金額區間 |
| POST | `/workbench/credit/tier/page` | 獎品檔位分頁 |
| POST | `/workbench/credit/tier/save` | 新增獎品檔位 |
| POST | `/workbench/credit/tier/update` | 修改獎品檔位 |
| POST | `/workbench/credit/tier/drop/{id}` | 刪除獎品檔位 |
| POST | `/workbench/credit/balance/page` | 積分餘額分頁 |
| GET | `/workbench/credit/balance/export` | 積分餘額匯出 |
| POST | `/workbench/credit/move/page` | 積分明細分頁 |
| GET | `/workbench/credit/move/export` | 積分明細匯出 |
| POST | `/workbench/credit/redeem/page` | 兌換紀錄分頁 |
| GET | `/workbench/credit/redeem/export` | 兌換紀錄匯出 |
| POST | `/workbench/shareback/config/page` | 返佣配置分頁 |
| POST | `/workbench/shareback/config/save` | 新增返佣配置 |
| POST | `/workbench/shareback/config/update` | 修改返佣配置 |
| GET | `/workbench/shareback/config/tiers` | 邀請等級查詢 |
| POST | `/workbench/shareback/record/page` | 返佣紀錄分頁 |
| GET | `/workbench/shareback/record/export` | 返佣紀錄匯出 |
| POST | `/workbench/shareback/daily/page` | 返佣日報分頁 |
| GET | `/workbench/shareback/daily/export` | 返佣日報匯出 |

## 公告、工單、內容、商城、排程

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| POST | `/workbench/notice/page` | 公告分頁 |
| GET | `/workbench/notice/peek/{id}` | 公告本文 |
| POST | `/workbench/notice/save` | 發布公告 |
| POST | `/workbench/notice/update` | 修改公告 |
| DELETE | `/workbench/notice/drop/{id}` | 刪除公告 |
| POST | `/workbench/roster/page` | 值班人員分頁 |
| GET | `/workbench/roster/on-duty` | 目前值班 |
| GET | `/workbench/roster/{id}` | 人員資料 |
| PUT | `/workbench/roster/{id}/presence` | 更新在線狀態 |
| GET | `/workbench/case/page` | 工單分頁 |
| GET | `/workbench/case/{id}` | 工單詳情 |
| POST | `/workbench/case` | 建立工單 |
| POST | `/workbench/case/{id}/assign` | 分派工單 |
| POST | `/workbench/case/{id}/comment` | 工單留言 |
| POST | `/workbench/case/{id}/close` | 結案 |
| GET | `/workbench/trail/page` | 稽核分頁 |
| GET | `/workbench/trail/{id}` | 稽核詳情 |
| POST | `/workbench/mail-tpl/page` | 郵件模板分頁 |
| GET | `/workbench/mail-tpl/peek/{id}` | 郵件模板詳情 |
| POST | `/workbench/mail-tpl/save` | 新增郵件模板 |
| POST | `/workbench/mail-tpl/update` | 修改郵件模板 |
| DELETE | `/workbench/mail-tpl/drop/{id}` | 刪除郵件模板 |
| POST | `/workbench/blast/save` | 新增批量推送 |
| POST | `/workbench/blast/page` | 批量推送分頁 |
| GET | `/workbench/blast/peek/{id}` | 批量推送詳情 |
| POST | `/workbench/blast/update` | 修改批量推送 |
| POST | `/workbench/blast/push` | 執行推送 |
| POST | `/workbench/blast/excel` | Excel 推送 |
| POST | `/workbench/blast/job` | 排程推送 |
| POST | `/workbench/blast/record-page` | 推送紀錄 |
| GET | `/workbench/blast/template` | 下載推送導入模板 |
| POST | `/workbench/story/save` | 保存內容 |
| POST | `/workbench/story/page` | 內容列表 |
| GET | `/workbench/story/peek` | 內容詳情 |
| GET | `/workbench/story/off` | 下架 |
| POST | `/workbench/strip/page` | 橫幅分頁 |
| POST | `/workbench/strip/save` | 新增橫幅 |
| POST | `/workbench/strip/update` | 修改橫幅 |
| GET | `/workbench/strip/reload` | 刷新橫幅快取 |
| POST | `/workbench/discover/list` | 探索頁列表 |
| GET | `/workbench/discover/peek/{id}` | 探索頁詳情 |
| POST | `/workbench/discover/add` | 新增探索頁 |
| POST | `/workbench/discover/update` | 編輯探索頁 |
| POST | `/workbench/discover/drop` | 刪除探索頁 |
| POST | `/workbench/entry/page` | 功能入口分頁 |
| POST | `/workbench/entry/save` | 新增功能入口 |
| POST | `/workbench/entry/update` | 修改功能入口 |
| POST | `/workbench/entry/drop` | 刪除功能入口 |
| POST | `/workbench/job-tpl/page` | 任務配置分頁 |
| GET | `/workbench/job-tpl/peek/{id}` | 任務配置詳情 |
| POST | `/workbench/job-tpl/save` | 新增任務配置 |
| POST | `/workbench/job-tpl/update` | 修改任務配置 |
| POST | `/workbench/job-tpl/drop/{id}` | 刪除任務配置 |
| POST | `/workbench/job-tpl/toggle` | 啟用或停用任務 |
| POST | `/workbench/job-user/page` | 使用者任務紀錄 |
| GET | `/workbench/job-user/stats/{taskId}` | 任務統計 |
| POST | `/workbench/shop/item/page` | 商城商品分頁 |
| POST | `/workbench/shop/item/save` | 新增商品 |
| GET | `/workbench/shop/item/peek/{productId}` | 商品詳情 |
| POST | `/workbench/shop/order/page` | 商城訂單分頁 |
| GET | `/workbench/shop/order/export` | 商城訂單匯出 |
| POST | `/workbench/shop/order/confirm-pay` | 確認付款 |
| POST | `/workbench/invest/product/page` | 理財產品分頁 |
| POST | `/workbench/invest/product/update` | 修改理財產品 |
| POST | `/workbench/invest/report/page` | 理財日報分頁 |
| GET | `/workbench/invest/report/export` | 理財日報匯出 |
| POST | `/workbench/cron/page` | 排程分頁 |
| GET | `/workbench/cron/{id}` | 排程資訊 |
| POST | `/workbench/cron` | 保存排程 |
| PUT | `/workbench/cron` | 修改排程 |
| DELETE | `/workbench/cron` | 刪除排程 |
| PUT | `/workbench/cron/run` | 立即執行 |
| PUT | `/workbench/cron/pause` | 暫停 |
| PUT | `/workbench/cron/resume` | 恢復 |
| POST | `/workbench/daily-sheet/list` | 營運日報分頁 |
| GET | `/workbench/daily-sheet/export` | 營運日報匯出 |
