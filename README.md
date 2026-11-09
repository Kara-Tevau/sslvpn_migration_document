# Tevau 企業 SSLVPN

公司內網服務改由 SSLVPN 接入。先連閘道，再以本機代理存取內網後台、原始碼與建置系統。直連舊內網會陸續關閉。用戶端由閘道頁面下載，原始碼不在本倉庫。

## 入口

兩個網域同一套帳號：

- https://sslvpn.tevau.io
- https://sslvpn.tevaupay.com

登入與登入後操作見 [ONBOARDING.md](ONBOARDING.md)。

## 文件

| 文件 | 內容 |
| --- | --- |
| [ONBOARDING.md](ONBOARDING.md) | 登入；登入後依頁面指示 |
| [docs/intranet-services.md](docs/intranet-services.md) | 內網服務總表（唯一清單） |
| [docs/migration.md](docs/migration.md) | 切換原則與日程 |
| [docs/services/](docs/services/) | 各系統介面地圖 |

介面地圖為什麼放在本倉庫，見 [docs/services/README.md](docs/services/README.md)。密碼與 Token 不進 git。
