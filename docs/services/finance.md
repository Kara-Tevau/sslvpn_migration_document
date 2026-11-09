# 財務管理系統

Base：`http://192.168.13.166:80`

帳單、對帳、台帳、費率與法幣。僅財務與獲授權營運帳號。匯出含敏感欄位，不要存到個人雲端。

列表多為 POST 分頁，匯出為 GET。對客服話術不要展開發行行名稱。

## 鑑權

與管理後台相同風格：帳密換隨機 token，Redis 存對應，**不是 JWT**。Header：

```http
POST /finx/session/open
Content-Type: application/json
Authorization: <token>
```

部門資料範圍生效：未授權部門的帳戶與帳單不會出現在分頁結果。

```sh
curl -sS 'http://192.168.13.166:80/finx/session/open' \
  -H 'Content-Type: application/json' \
  -d '{"username":"YOUR_USERNAME","password":"YOUR_PASSWORD"}'
```

## 工作階段

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| POST | `/finx/session/open` | 登入。Body：username、password |
| POST | `/finx/session/close` | 退出 |
| GET | `/finx/session/me` | 目前登入者 |

## 帳單與卡帳戶

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| POST | `/finx/wallet-move/holder` | 客戶錢包帳戶變動 |
| POST | `/finx/wallet-move/platform` | 平台帳戶錢包變動 |
| POST | `/finx/wallet-move/credit` | 充值變動 |
| POST | `/finx/wallet-move/debit` | 提現變動 |
| GET | `/finx/wallet-move/holder-export` | 客戶錢包變動匯出 |
| GET | `/finx/wallet-move/platform-export` | 平台錢包變動匯出 |
| POST | `/finx/modulr/main-page` | Modulr 主帳戶明細 |
| GET | `/finx/modulr/main-export` | Modulr 主帳戶匯出 |
| POST | `/finx/modulr/frost-page` | Modulr 凍結帳戶明細 |
| GET | `/finx/modulr/frost-export` | Modulr 凍結帳戶匯出 |
| POST | `/finx/modulr/media-page` | Modulr 卡帳單 |
| GET | `/finx/modulr/media-export` | Modulr 卡帳單匯出 |
| POST | `/finx/clearbank/book-page` | ClearBank 帳單 |
| GET | `/finx/clearbank/book-export` | ClearBank 帳單匯出 |
| POST | `/finx/plastic-move/page` | 客戶卡變動分頁 |
| POST | `/finx/plastic-open/page` | 開卡分頁 |
| POST | `/finx/plastic-close/page` | 銷卡分頁 |
| POST | `/finx/plastic-open-log/page` | 開卡紀錄分頁 |
| POST | `/finx/plastic-close-log/page` | 銷卡紀錄分頁 |
| POST | `/finx/inflight/page` | 在途資金分頁 |
| GET | `/finx/plastic-move/export` | 卡帳戶變動匯出 |
| GET | `/finx/inflight/export` | 在途資金匯出 |
| GET | `/finx/plastic-open/export` | 開卡匯出 |
| GET | `/finx/plastic-close/export` | 銷卡匯出 |
| GET | `/finx/plastic-open-log/export` | 開卡紀錄匯出 |
| GET | `/finx/plastic-close-log/export` | 銷卡紀錄匯出 |
| POST | `/finx/lhv/move-page` | LHV 卡變動分頁 |
| GET | `/finx/lhv/move-export` | LHV 卡變動匯出 |

## 資金、歸集、線下

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| POST | `/finx/vault/collect-page` | 歸集紀錄 |
| POST | `/finx/vault/withdraw-page` | 提幣紀錄 |
| POST | `/finx/vault/multisig-page` | 多簽紀錄 |
| POST | `/finx/vault/sell-page` | 卡銷售收款 |
| POST | `/finx/vault/partners` | 合作渠道方 |
| POST | `/finx/vault/uncollected` | 未歸集資料 |
| POST | `/finx/vault/fee-page` | 手續費帳戶 |
| POST | `/finx/vault/holder-page` | 使用者帳戶 |
| POST | `/finx/vault/chain` | 客戶鏈上地址 |
| GET | `/finx/vault/export-partner` | 匯出渠道方 |
| GET | `/finx/vault/export-collect` | 匯出歸集 |
| GET | `/finx/vault/export-withdraw` | 匯出提幣 |
| GET | `/finx/vault/export-multisig` | 匯出多簽 |
| GET | `/finx/vault/export-sell` | 匯出卡銷售 |
| GET | `/finx/vault/export-uncollected` | 匯出未歸集 |
| GET | `/finx/vault/export-fee` | 匯出手續費 |
| GET | `/finx/vault/export-holder` | 匯出使用者帳戶 |
| GET | `/finx/vault/export-chain` | 匯出鏈上地址 |
| GET | `/finx/vault/balance` | 查詢餘額 |
| POST | `/finx/media-book/modulr-page` | Modulr 卡帳戶列表 |
| POST | `/finx/media-book/clearbank-page` | ClearBank 卡帳戶列表 |
| GET | `/finx/media-book/modulr-export` | Modulr 匯出 |
| GET | `/finx/media-book/clearbank-export` | ClearBank 匯出 |
| POST | `/finx/media-book/list` | 卡帳戶分頁 |
| POST | `/finx/media-book/save` | 保存卡帳戶 |
| GET | `/finx/media-book/drop` | 刪除卡帳戶 |
| GET | `/finx/media-book/export` | 卡帳戶匯出 |
| POST | `/finx/offline/page` | 線下列表 |
| POST | `/finx/offline/by-addr` | 依地址查線下 |
| POST | `/finx/offline/save` | 新增線下 |
| GET | `/finx/offline/export` | 線下匯出 |
| POST | `/finx/offline/import` | 線下導入 |
| POST | `/finx/shift/page` | 線下調帳分頁 |
| POST | `/finx/shift/save` | 新增調帳 |
| DELETE | `/finx/shift/drop/{id}` | 刪除調帳 |
| GET | `/finx/shift/export` | 調帳匯出 |
| POST | `/finx/shift/import` | 調帳導入 |
| POST | `/finx/bank-map/page` | 法幣銀行分頁 |
| POST | `/finx/bank-map/update` | 修改法幣銀行 |
| POST | `/finx/bank-line/save` | 新增法幣子項 |
| DELETE | `/finx/bank-line/drop/{id}` | 刪除法幣子項 |
| GET | `/finx/bank-line/by-code` | 依代碼查子項 |
| POST | `/finx/bank-line/page` | 法幣子項分頁 |
| GET | `/finx/bank-line/export` | 法幣子項匯出。type=2 模板，type=1 資料 |
| POST | `/finx/bank-line/import` | 法幣子項導入 |
| POST | `/finx/allow/page` | 財務白名單分頁 |
| GET | `/finx/allow/template` | 白名單模板 |
| POST | `/finx/allow/import` | 白名單導入 |
| DELETE | `/finx/allow/drop/{id}` | 刪除白名單 |
| POST | `/finx/inflow/page` | 收入列表 |
| GET | `/finx/inflow/export` | 收入匯出 |
| POST | `/finx/outflow/page` | 支出列表 |
| GET | `/finx/outflow/export` | 支出匯出 |
| POST | `/finx/sum/page` | 日彙總列表 |
| GET | `/finx/sum/export` | 日彙總匯出 |
| POST | `/finx/platform-stat/page` | 平台資金列表 |
| GET | `/finx/platform-stat/export` | 平台資金匯出 |
| POST | `/finx/side-acct/page` | 其他帳戶分頁 |
| DELETE | `/finx/side-acct/drop/{id}` | 刪除其他帳戶 |
| POST | `/finx/side-acct/save` | 保存其他帳戶 |
| GET | `/finx/side-acct/export` | 其他帳戶匯出 |
| POST | `/finx/platform-acct/page` | 平台帳戶管理分頁 |
| POST | `/finx/daily-snap/page` | 每日帳戶資訊分頁 |
| POST | `/finx/daily-snap/save` | 新增每日帳戶資訊 |
| POST | `/finx/issuer-acct/page` | 發行行帳戶分頁 |
| POST | `/finx/issuer-acct/save` | 新增發行行帳戶 |
| POST | `/finx/issuer-acct/update` | 修改發行行帳戶 |
| DELETE | `/finx/issuer-acct/drop/{accountId}` | 刪除發行行帳戶 |

## 費率、匯率、法幣

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| POST | `/finx/tx-fee/page` | 交易手續費配置分頁 |
| GET | `/finx/tx-fee/peek/{configId}` | 交易手續費詳情 |
| POST | `/finx/tx-fee/save` | 新增交易手續費 |
| POST | `/finx/tx-fee/update` | 修改交易手續費 |
| DELETE | `/finx/tx-fee/drop/{configId}` | 刪除交易手續費 |
| GET | `/finx/tx-fee/reload-media` | 刷新卡相關手續費快取 |
| POST | `/finx/pipe-fee/page` | 渠道手續費分頁 |
| GET | `/finx/pipe-fee/peek/{configId}` | 渠道手續費詳情 |
| POST | `/finx/pipe-fee/save` | 新增渠道手續費 |
| POST | `/finx/pipe-fee/update` | 修改渠道手續費 |
| DELETE | `/finx/pipe-fee/drop/{configId}` | 刪除渠道手續費 |
| GET | `/finx/pipe-fee/reload` | 刷新渠道手續費快取 |
| POST | `/finx/month-fee/page` | 月費管理分頁 |
| GET | `/finx/month-fee/export` | 月費匯出 |
| POST | `/finx/month-fee/lines/page` | 月費詳情分頁 |
| GET | `/finx/month-fee/lines/export` | 月費詳情匯出 |
| POST | `/finx/fx/page` | 匯率分頁 |
| GET | `/finx/fx/platform` | 取得平台對應匯率 |
| GET | `/finx/fx/reload/{platformType}` | 刷新指定平台匯率 |
| GET | `/finx/fx/reload` | 刷新全部匯率 |
| POST | `/finx/fx/save` | 新增匯率 |
| POST | `/finx/fx/after-credit` | 計算充值後匯率 |
| POST | `/finx/cash/credit-page` | 法幣充值分頁 |
| GET | `/finx/cash/credit-export` | 法幣充值匯出 |
| POST | `/finx/cash/credit-one` | 法幣充值詳情 |
| POST | `/finx/cash/debit-page` | 法幣提現分頁 |
| GET | `/finx/cash/debit-export` | 法幣提現匯出 |
| POST | `/finx/cash/convert-page` | 幣種兌換分頁 |
| GET | `/finx/cash/convert-export` | 幣種兌換匯出 |
| POST | `/finx/cash/patch` | 法幣補單 |
| POST | `/finx/cash/giveback-page` | 法幣退款任務分頁 |
| GET | `/finx/cash/giveback/{taskId}` | 法幣退款詳情 |
| POST | `/finx/cash/giveback-run` | 執行法幣退款 |
| POST | `/finx/cash/rfi-page` | RFI 分頁 |
| POST | `/finx/cash/rfi-one` | RFI 詳情 |
| POST | `/finx/cash/rfi-send` | 提交 RFI |
| POST | `/finx/cash/cap-page` | 法幣限額分頁 |
| POST | `/finx/cash/cap-one` | 法幣限額詳情 |
| POST | `/finx/cash/cap-save` | 保存法幣限額 |
