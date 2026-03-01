# Microsoft Graph API（curl）でのEntra ID ユーザー一覧情報取得について

## 参考公式記事

※ 本説明の根拠となる Microsoft 公式ドキュメント

1. **Microsoft Graph API 概要**
   [https://learn.microsoft.com/ja-jp/graph/use-the-api](https://learn.microsoft.com/ja-jp/graph/use-the-api)

2. **OData クエリ パラメータ（$select / $filter 等）**
   [https://learn.microsoft.com/ja-jp/graph/query-parameters](https://learn.microsoft.com/ja-jp/graph/query-parameters)

3. **ページング（@odata.nextLink）公式説明**
   [https://learn.microsoft.com/ja-jp/graph/paging](https://learn.microsoft.com/ja-jp/graph/paging)

4. **Microsoft Graph API ベストプラクティス**
   [https://learn.microsoft.com/ja-jp/graph/best-practices-concept](https://learn.microsoft.com/ja-jp/graph/best-practices-concept)

5. **ユーザー差分取得（delta query）**
   [https://learn.microsoft.com/ja-jp/graph/delta-query-users](https://learn.microsoft.com/ja-jp/graph/delta-query-users)

---

## 1. 目的（何をしたいか）

Microsoft Entra ID（旧 Azure AD）に登録されている
**ユーザー情報（一覧・名前・ログインIDなど）を Microsoft Graph API 経由で取得**し、
社内システム連携や帳票・データ処理に利用する。

---

## 2. 全体の処理フロー（curl 前提）

1. Entra ID にアプリ登録
2. Microsoft Graph API の権限を付与

   * 例：`User.Read.All`
3. OAuth 2.0 により **アクセストークン（Bearer Token）を取得**
4. curl を使って Graph API にリクエスト送信
5. JSON レスポンスを処理

---

## 3. ユーザー情報の取得方法

### ① カスタマイズなし（基本形）

```bash
curl -X GET \
  https://graph.microsoft.com/v1.0/users \
  -H "Authorization: Bearer {access_token}"
```

**特徴**

* 既定のユーザー属性が返る
* 1回のレスポンスは **最大100件**
* ユーザー数が多い場合は **ページングが発生**

---

### ② カスタマイズあり（実務向き）

#### 必要な属性だけ取得（$select）

```bash
curl -X GET \
  "https://graph.microsoft.com/v1.0/users?\$select=id,displayName,userPrincipalName" \
  -H "Authorization: Bearer {access_token}"
```

#### 条件指定（$filter）

```bash
curl -X GET \
  "https://graph.microsoft.com/v1.0/users?\$filter=accountEnabled eq true" \
  -H "Authorization: Bearer {access_token}"
```

---

## 4. ページング（@odata.nextLink）の考え方【重要】

### なぜ必要か

* Graph API は大量データを一度に返さない設計
* **100件を超えると nextLink による分割取得になる**

---

### レスポンス例（イメージ）

```json
{
  "value": [
    { "displayName": "山田 太郎" }
  ],
  "@odata.nextLink": "https://graph.microsoft.com/v1.0/users?$skiptoken=xxxxx"
}
```

* `value`：現在ページのユーザー情報
* `@odata.nextLink`：**次のページ取得用 URL**

---

## 5. リクエスト側での実装方針（重要ポイント）

### 結論

**リクエスト側で nextLink を記載・定義する必要はありません。**

### やること

1. 初回リクエストを送信
2. レスポンスに `@odata.nextLink` があれば、その URL に GET
3. nextLink がなくなるまで繰り返す

### やらないこと

* `$skip` の自前指定
* nextLink URL の加工・編集
* ページ番号管理

👉 ページ管理は **すべて Microsoft Graph 側が実施**

---

## 6. curl を使ったページング処理イメージ

```
URL = 初回リクエストURL

while (URL が存在する) {
  curl で GET
  レスポンスの value を処理
  URL = レスポンスの @odata.nextLink
}
```

### 手動実行イメージ

```bash
curl -H "Authorization: Bearer {access_token}" \
https://graph.microsoft.com/v1.0/users
```

```bash
curl -H "Authorization: Bearer {access_token}" \
"https://graph.microsoft.com/v1.0/users?$skiptoken=xxxxx"
```

---

## 7. ユーザーの「名前」はどの項目か？

### プロパティの正しい理解

| プロパティ             | 内容      | 用途      |
| ----------------- | ------- | ------- |
| displayName       | 表示名（氏名） | 画面表示・帳票 |
| userPrincipalName | ログインID  | 認証・識別   |
| id                | 内部一意ID  | システム連携  |

👉 **名前として使うのは `displayName`**
👉 `userPrincipalName` は名前ではない

---

## 8. 実装上のベストプラクティス

* `$select` で必要な項目のみ取得（通信量削減）
* ページング（nextLink）前提で設計
* HTTP 429（スロットリング）時のリトライ考慮
* 内部キーは `id` または `userPrincipalName`

---

## 9. 補足：差分取得（delta query）

* 毎回全件取得せず、**変更分のみ取得**可能
* 定期バッチや同期処理向け

---

## 10. まとめ

Entra ID のユーザー情報は Microsoft Graph API を使い、
curl で REST API を呼び出して取得します。
ユーザー数が多い場合はレスポンスに含まれる `@odata.nextLink` を利用して
分割取得を行い、ページ管理は Graph 側が担当します。
表示用の名前は `displayName` を使用します。