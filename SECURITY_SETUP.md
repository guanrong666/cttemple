# Firebase 安全部署設定

前端網站中的 Firebase `apiKey` 不是密碼，公開是正常的。真正的安全邊界是
Firebase Authentication 與 Firestore Security Rules。

## 第一次設定

1. 在 Firebase Console 的 **Authentication > Sign-in method** 啟用
   **Email/Password**。
2. 在 **Authentication > Users** 建立第一個管理員帳號，複製該帳號的 UID。
3. 在 Firestore Console 建立文件 `staff/{管理員 UID}`：

   ```json
   {
     "email": "管理員登入 Email",
     "role": "admin"
   }
   ```

4. 發布本專案的 `firestore.rules`：

   ```bash
   firebase deploy --only firestore:rules
   ```

5. 在 Firebase Authentication 的授權網域加入 `guanrong666.github.io`。
6. 使用管理員 Email 與密碼登入網站。新增其他員工時，先在 Authentication
   建立帳號，再到網站的「帳號管理」填入該帳號 UID、Email 與角色。

## 舊資料清理

- 立刻更改曾經寫在前端或 Firestore `staff` 文件中的所有舊密碼。
- 刪除舊版以帳號名稱為文件 ID、且包含 `password` 欄位的 `staff` 文件。
- 新版 `staff` 文件必須以 Firebase Authentication UID 為文件 ID，且不得包含
  `password` 欄位。

## 注意

- 不要把 Firebase service-account JSON、私鑰或 Admin SDK 憑證放到 GitHub Pages。
- 前端的角色判斷只控制畫面；真正的讀寫權限由 `firestore.rules` 強制執行。
- Firebase API key 可以另外設定 HTTP referrer 與 API restrictions，但不能取代
  Authentication 與 Security Rules。
- 在完成第 1 至第 3 步之前不要發布規則，否則尚未建立管理員角色時，網站會拒絕
  所有資料存取。
