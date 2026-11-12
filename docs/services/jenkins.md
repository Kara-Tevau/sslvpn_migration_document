# Jenkins

Base：`http://192.168.13.204:80`

持續整合。閘道帳號不能代替 Jenkins 帳號。請用瀏覽器開啟根路徑。

## 鑑權

反向代理強制 HTTP Basic，realm 為：

```http
WWW-Authenticate: Basic realm="Tevau Internal Service", charset="UTF-8"
```

瀏覽器第一次開啟會跳出帳密視窗。腳本對 Base 帶：

```http
Authorization: Basic base64(username:password)
```

未帶憑證或憑證錯誤時回應 `401`，本文：

```text
密碼錯誤。
```

通過 Basic 之後，Jenkins 本身仍使用自己的使用者資料庫。寫入類操作另需該使用者在 Jenkins 內的權限。Job 拉程式碼時，Git 遠端使用 `http://192.168.13.119/`。
