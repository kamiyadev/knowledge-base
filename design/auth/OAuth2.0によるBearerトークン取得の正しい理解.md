# OAuth2.0 (Bearer) によるアクセストークン取得方式の整理  

## 1. 背景・目的

Microsoft Graph や Microsoft Identity Platform を利用する際、  
**アクセストークン（Bearer Token）をどの方式で取得するのが正しいのか**、  
また **OAuth2.0 以外の方法が存在するのか** を整理し、  
上司・関係者が納得できる形で説明することを目的とする。

本資料は以下 3 つの Microsoft 公式ドキュメントを基にまとめている。

- https://learn.microsoft.com/ja-jp/entra/identity-platform/v2-oauth2-client-creds-grant-flow
- https://learn.microsoft.com/ja-jp/graph/auth/auth-concepts
- https://learn.microsoft.com/ja-jp/graph/auth-v2-service

---

## 2. 各ドキュメントの要点整理

### 2.1 OAuth2.0 クライアント資格情報フロー（Client Credentials Flow）

#### 概要

- ユーザーを介さず、アプリケーション自身が認証する OAuth2.0 フロー
- サーバー間通信（M2M: Machine to Machine）向け
- バッチ処理、バックエンド API、デーモンアプリなどで利用

#### 特徴

- 認証情報
  - `client_id`
  - `client_secret` または 証明書
- `/token` エンドポイントへ POST
- `grant_type=client_credentials`
- **Bearer 型アクセストークンを取得**
- `refresh_token` は発行されない（期限切れ時は再取得）

---

### 2.2 Microsoft Graph 認証と承認の基本

#### 概要

Microsoft Graph で API を呼び出す際の  
**認証・承認の全体像** を説明したドキュメント。

#### 重要ポイント

- Microsoft Graph は **OAuth 2.0 を前提** とした API
- アクセス方式は 2 種類
  - **委任されたアクセス**
    - ユーザーの代わりにアプリが操作
  - **アプリ専用アクセス**
    - アプリ自身の権限で操作
- API 呼び出し時は必ず以下形式

```http
Authorization: Bearer {access_token}
```

---

### 2.3 Microsoft Graph（ユーザーなし）アクセストークン取得

#### 概要

* Client Credentials フローを利用し
* **ユーザーなしで Microsoft Graph にアクセスする具体的手順** を示したドキュメント

#### ポイント

* `/oauth2/v2.0/token` エンドポイントに POST
* `scope=https://graph.microsoft.com/.default`
* 成功時に Bearer トークンが返却される
* このトークンを使って Graph API を呼び出す

---

## 3. OAuth2.0 フロー比較表

| 項目            | Client Credentials | Authorization Code | On-Behalf-Of (OBO) |
| ------------- | ------------------ | ------------------ | ------------------ |
| 利用シーン         | サーバー間 / バッチ        | ユーザー操作あり           | 中間 API             |
| ユーザー関与        | なし                 | あり                 | あり                 |
| 認証主体          | アプリ                | ユーザー + アプリ         | ユーザー + API         |
| grant_type    | client_credentials | authorization_code | jwt-bearer         |
| Bearer Token  | ◯                  | ◯                  | ◯                  |
| Refresh Token | ×                  | ◯                  | ◯                  |
| Redirect URI  | 不要                 | 必須                 | 不要                 |
| 主な用途          | M2M / 自動処理         | Web / SPA          | マイクロサービス           |

---

## 4. 実装例

### 4.1 Client Credentials でアクセストークン取得（cURL）

```bash
curl -X POST https://login.microsoftonline.com/{tenant_id}/oauth2/v2.0/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=YOUR_CLIENT_ID" \
  -d "client_secret=YOUR_CLIENT_SECRET" \
  -d "scope=https://graph.microsoft.com/.default" \
  -d "grant_type=client_credentials"
```

#### レスポンス例

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJh..."
}
```

---

### 4.2 Microsoft Graph API 呼び出し例

```bash
curl -H "Authorization: Bearer {access_token}" \
     https://graph.microsoft.com/v1.0/users
```

---

## 5. OAuth2.0 以外にアクセストークン取得方法はあるか？

### 結論

**Microsoft Identity Platform において、
アクセストークンを取得する正式な方法は OAuth2.0 のみである。**

---

### 理由

* Microsoft は OAuth 2.0 を認可基盤として採用している
* 発行されるアクセストークンは **常に Bearer Token**
* OpenID Connect は「認証（ID Token）」用であり、アクセストークンとは別物
* Microsoft Graph は OAuth2.0 トークンを前提として設計されている

---

### 例外的に見えるケースについて

| 方式                           | 実態                    |
| ---------------------------- | --------------------- |
| Managed Identity             | 内部的に OAuth2.0 を使用     |
| Workload Identity Federation | 最終的に OAuth2.0 トークンを取得 |
| Azure SDK の自動認証              | SDK 内部で OAuth2.0 実行   |

➡ **見えなくなっているだけで、OAuth2.0 を使っていないケースは存在しない**

---

## 6. まとめ

* Microsoft Graph / Entra ID におけるアクセストークン取得は **OAuth2.0 が唯一の標準**
* ユーザーなしのアクセスには **Client Credentials フロー** を使用する
* Bearer トークンを Authorization ヘッダーに付与する方式が必須
* Azure の便利機能も内部では OAuth2.0 を利用している
* よって **「OAuth2.0 以外の方法でアクセストークンを取得することはできない」**