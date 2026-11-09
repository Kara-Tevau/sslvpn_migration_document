# 內網服務遷移至 SSLVPN

## 目標

公司內部 HTTP 服務改由 Tevau 企業 SSLVPN 接入。公網只保留閘道：

- https://sslvpn.tevau.io
- https://sslvpn.tevaupay.com

應用不再對辦公室網路或舊 VPN 網段直接暴露。本階段遷移哪些服務、新 Base 與遷移前地址，只寫在 [intranet-services.md](intranet-services.md)。

書籤、腳本、監控探針請改成總表裡的新 Base，不要再直連遷移前地址，也不要把內網 IP 寫進前端公開設定。各系統登入與路徑見 [services/README.md](services/README.md)。

## 切換原則

1. 先連 SSLVPN，再打內網 HTTP。
2. HTTPS 終止在閘道，內網維持 HTTP。
3. 內網主機名尚未切到公司 DNS，文件與 CI 變數寫總表裡的 Base。
4. 閘道帳號與各後台帳號分開。閘道只解決「能不能進網段」；各系統仍用自己的登入。
5. GitLab 與 Jenkins 的 Webhook、Runner、部署機也必須走用戶端或專用出口，禁止假設它們仍在舊網段。

## 連線檢查

用戶端起來、代理指到 `127.0.0.1` 之後，用 [intranet-services.md](intranet-services.md) 的 Base 做一次 GET。未開用戶端時那些位址不可達，這是預期行為。

## 日程

| 階段 | 內容 |
| --- | --- |
| 已上線 | 閘道雙網域、用戶端下載、總表所列內網 HTTP |
| 進行中 | 各業務把回呼與排程改到新 Base |
| 後續 | 關閉舊內網入口；補內網 DNS |

切換期間若某主機暫時沒有應用行程，反向代理可能回 502。請稍後再試，不要改回遷移前地址。
