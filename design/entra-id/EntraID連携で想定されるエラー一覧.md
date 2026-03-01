# Entra ID と中間サーバ連携における想定エラー一覧  

## 概要
中間サーバ（Linux / Ubuntu）から Microsoft Entra ID（旧 Azure AD）に対して  
Microsoft Graph API を使用しユーザー一覧を取得するシステムにおいて、  
認証・認可〜取得処理までに想定されるエラーを整理する。

---

## ① 認証（Authentication）関連エラー

### 1. トークン取得失敗
- `invalid_client`
  - Client ID が誤っている
  - アプリ登録が削除・無効化されている
- `invalid_client_secret`
  - クライアントシークレット誤り
  - シークレットの有効期限切れ
- `invalid_grant`
  - tenant_id が誤り
  - リフレッシュトークン失効
- `unauthorized_client`
  - 許可されていない認証フローを使用している

### 2. 認証方式の不整合
- Client Credentials Flow で Delegated 権限を要求
- 対話型ログインが必要なフローを非対話環境で使用

---

## ② 認可（Authorization / Permission）関連エラー

### 3. API 権限不足
- `Authorization_RequestDenied`
  - `User.Read.All` / `Directory.Read.All` 未付与
- Application 権限に対する Admin Consent 未実施
- Delegated 権限でサインインユーザーが管理者でない

### 4. 権限種別の不一致
- Application 権限が必要だが Delegated 権限のみ設定
- `GET /users` に対し `User.Read` のみ付与

---

## ③ Microsoft Graph API 呼び出しエラー

### 5. HTTP ステータスコード系
- **401 Unauthorized**
  - トークン期限切れ
  - Authorization ヘッダー未設定
- **403 Forbidden**
  - 権限不足
  - Conditional Access によりブロック
- **404 Not Found**
  - API エンドポイント誤り
- **429 Too Many Requests**
  - レートリミット（スロットリング）超過
- **500 / 503**
  - Microsoft Graph 側の一時障害

---

## ④ トークン・セキュリティ関連

### 6. トークン有効期限
- access_token の期限切れ
- 長時間処理中の失効
- トークンキャッシュ未更新

### 7. Conditional Access（条件付きアクセス）
- IP 制限により中間サーバが拒否
- MFA 必須ポリシーにより Service Principal がブロック
- 特定アプリのみアクセス許可

---

## ⑤ ネットワーク・インフラ関連（Linux / Ubuntu）

### 8. ネットワーク疎通
- DNS 解決不可  
  - `login.microsoftonline.com`
  - `graph.microsoft.com`
- Firewall による HTTPS(443) ブロック
- TLS/SSL 証明書エラー

### 9. Proxy・証明書
- Proxy 認証エラー
- Proxy による証明書検証失敗
- CA 証明書不足（`ca-certificates` 未導入）

---

## ⑥ データ取得・処理ロジック関連

### 10. ページング未対応
- `@odata.nextLink` 未処理
- 一部ユーザーのみ取得される
- 大規模テナントで件数制限に到達

### 11. クエリ・フィルタ指定ミス
- `$filter` 構文誤り
- 使用不可プロパティ指定
- `ConsistencyLevel: eventual` 未指定

---

## ⑦ Entra ID 側の状態起因

### 12. テナント状態
- テナント一時ロック
- サービス障害
- アプリ登録削除・無効化

### 13. ユーザー属性不整合
- Null 値未考慮
- Guest ユーザー混在
- 文字コード問題（UPN / DisplayName）

---

## ⑧ 運用・保守フェーズでの想定エラー

### 14. 設定変更影響
- API 権限削除
- クライアントシークレット期限切れ
- Graph API バージョン変更影響

### 15. ログ・監査
- サインインログにエラー記録
- サービスプリンシパル認証失敗
- 監査ログ未取得

---

## ⑨ 代表的な Microsoft Graph API エラーコード

| エラーコード | 内容 |
|-------------|------|
| invalid_client | Client ID 不正 |
| invalid_client_secret | シークレット誤り |
| Authorization_RequestDenied | 権限不足 |
| InvalidAuthenticationToken | トークン不正 |
| Request_Throttled | レート制限 |
| ResourceNotFound | リソース不存在 |

---

## まとめ
以下の観点は最低限考慮すること：
- 認証失敗
- 権限不足
- ネットワーク障害
- トークン期限切れ
- スロットリング
- ページング未対応
```