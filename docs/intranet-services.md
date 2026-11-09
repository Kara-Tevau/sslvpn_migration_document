# 內網服務總表

> 給已經能連上 SSLVPN、但還要把舊腳本改到新位址的人。閘道不管各系統帳號；路徑與鑑權見 [services/](services/README.md)。

前置：用戶端已連線，本機代理指向用戶端連接埠。未連線時下列 Base 不可達。

| 名稱 | Base | 遷移前地址 | 文件 |
| --- | --- | --- | --- |
| TevauPay 管理後台 | http://192.168.13.3:80 | https://tevau.io/tevau-admin/ | [services/tevaupay-admin.md](services/tevaupay-admin.md) |
| 統一身分管理 | http://192.168.13.47:80 | http://192.168.2.14 | [services/identity.md](services/identity.md) |
| 商戶管理平台 | http://192.168.13.89:80 | https://openapi.tevau.io/merchants | [services/merchant.md](services/merchant.md) |
| GitLab | http://192.168.13.119:80 | https://gitlab.tevaupay.com | [services/gitlab.md](services/gitlab.md) |
| 財務管理系統 | http://192.168.13.166:80 | http://192.168.2.31 | [services/finance.md](services/finance.md) |
| Jenkins | http://192.168.13.204:80 | https://jenkins.tevaupay.com | [services/jenkins.md](services/jenkins.md) |
| 營運管理平台 | http://192.168.13.231:80 | http://192.168.2.18 | [services/operations.md](services/operations.md) |

遷移前地址只給改腳本對照，切換後不要再直連。閘道登入不能代替各系統自己的登入。

GitLab 與 Jenkins 只記錄入口與鑑權。其餘系統的路徑見各文件。分頁與 token 約定見 [services/README.md](services/README.md)。

## 連線後的公開站

- https://docs.tevau.io/
- https://api-docs.tevau.io/
- https://ip.tevau.io/
