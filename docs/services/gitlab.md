# GitLab

Base：`http://192.168.13.119:80`

公司原始碼與合併請求放在這台 GitLab。閘道帳號不能直接當 GitLab 帳號。請用瀏覽器開啟根路徑。

## 鑑權

1. **網頁工作階段**  
   開啟 `http://192.168.13.119:80/`，使用 GitLab 使用者名稱與密碼登入。登入後瀏覽器帶 `_gitlab_session`。本階段不開放自行註冊。

2. **個人存取權杖**  
   登入後在 GitLab 介面建立 Personal Access Token。以 HTTP 存取儲存庫時，使用者名稱任意、密碼填權杖；或在請求 Header 帶：

   ```http
   PRIVATE-TOKEN: <token>
   ```

3. **遠端**  
   只提供 HTTP。先開 SSLVPN 用戶端，再 clone / push。SSH 本階段不提供。

權杖與密碼不要寫進本倉庫或聊天紀錄。Webhook、Runner 若要打其他內網服務，目標必須是 `192.168.13.0/24` 已列主機。
