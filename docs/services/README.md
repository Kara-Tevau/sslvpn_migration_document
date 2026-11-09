# 內網 API 對照 —— SSLVPN 切換備忘

> 為把公司內網改掛 SSLVPN 而整理的到達方式與介面地圖。
> 閘道只解決「能不能進網段」；各系統仍用自己的登入與路徑。腳本、Webhook、排程、監控探針要把舊 Base URL 換成總表裡的新位址，所以把目前在用的介面抄在本倉庫。

## 為什麼文件在這裡，而不是各業務倉庫

切換窗口裡，真正卡住的不是「VPN 連不上」，而是連上之後：

1. 辦公室網段與舊 VPN 會關掉。原本寫死內網 IP / 主機名的後台腳本、Jenkins Job、GitLab Webhook、對帳匯出、客服小工具會一次失效。
2. 閘道門戶只給圖示。GitLab、Jenkins 大家會用，不必再抄一份官方 API。其餘五個後台沒有在新網段提供獨立文件站；公開的 [api-docs.tevau.io](https://api-docs.tevau.io/) 是對客說明，不含內部營運路徑。
3. 基礎設施這邊通讀各服務目前在用的 Controller 映射，按新位址重排成一份對照，讓只拿到本倉庫的同仁也能改完 Base URL 與 Header，不必把每個 Java 倉庫都 clone 下來。

本倉庫**不是**業務源碼，也不是 Apifox 真源。路徑與欄位以切換當天能打通為準；業務規則仍以各服務自己的倉庫為準。密碼、Token、連線憑證不進 git。位址只寫在 [../intranet-services.md](../intranet-services.md)。

## 收錄範圍

| 文件 | 收什麼 |
| --- | --- |
| [tevaupay-admin.md](tevaupay-admin.md) | 營運後台：登入、系統設定、卡片／客戶／KYC／退款 |
| [identity.md](identity.md) | 目錄與 SSO：工作階段、部門角色、第三方登入 |
| [merchant.md](merchant.md) | 商戶臺 + 租戶閘道：營運登入、企業／KYB、開卡與查詢 |
| [gitlab.md](gitlab.md) | 只記入口與鑑權 |
| [finance.md](finance.md) | 帳單、歸集、費率、法幣 |
| [jenkins.md](jenkins.md) | 只記入口與鑑權（HTTP Basic） |
| [operations.md](operations.md) | 活動、積分、工單、內容、排程 |

GitLab / Jenkins 產品 API 以官方文件為準，這裡不重複。其餘五個系統收的是切換時腳本**會打到的入口**（登入、分頁、詳情、匯出、審核、回調），不是全量 Controller。切換原則與日程見 [../migration.md](../migration.md)。

## 怎麼用這份對照

1. 先連 SSLVPN，代理指到用戶端連接埠。未連線時總表裡的位址不可達，這是預期行為。
2. 用閘道帳號進網段；再用**該系統自己的帳號**換 token。兩套帳號不要混用。
3. 把舊腳本的 host 換成總表裡的新 Base，Header 按各文件的鑑權節改。路徑不要從記憶體裡的舊文件站抄。
4. 列表多為 POST Body 分頁（`page` 從 1、`pageSize` 預設 20）；匯出為 GET。詳情習慣 `peek/{id}`。
5. 業務系統 token 是服務端簽發的隨機會話，**不是 JWT**，不要本地驗簽。

## 已知未收（切換後不必堵在本倉庫）

- 各服務內部 Feign / 定時任務（不從員工筆電直連）。
- 對客 OpenAPI 的完整欄位表（仍看 api-docs.tevau.io）。
- 內網 DNS 主機名（尚未切到此網段，位址見總表）。
