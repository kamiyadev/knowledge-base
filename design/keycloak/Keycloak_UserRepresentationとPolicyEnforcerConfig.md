# Keycloak REST API: `UserRepresentation` と `PolicyEnforcerConfig`

Keycloak REST APIでよく出てくる `UserRepresentation` と `PolicyEnforcerConfig` について整理します。

---

## 1. `UserRepresentation`

`UserRepresentation` は **Keycloakのユーザー情報を表すオブジェクト** です。  
REST API では、ユーザーの作成・取得・更新・削除などで使用されます。

### 主なフィールド

| フィールド | 型 | 説明 |
|------------|----|------|
| `id` | string | Keycloak上でのユーザーID（内部ID） |
| `username` | string | ログイン用のユーザー名 |
| `email` | string | メールアドレス |
| `enabled` | boolean | ユーザーが有効かどうか |
| `firstName` | string | 名 |
| `lastName` | string | 姓 |
| `attributes` | object | カスタム属性（Key: Value の形式） |
| `realmRoles` | array | 割り当てられたRealmロールのリスト |
| `clientRoles` | object | クライアント単位でのロール割り当て |
| `credentials` | array | パスワードなどの認証情報（作成時のみ使用） |

### 例

```json
{
  "id": "12345678-1234-1234-1234-123456789abc",
  "username": "taro",
  "email": "taro@example.com",
  "enabled": true,
  "firstName": "Taro",
  "lastName": "Yamada",
  "attributes": {
    "department": "Sales"
  },
  "realmRoles": ["user", "admin"]
}
````

> `UserRepresentation` はユーザーの全情報を表現するDTOのようなものです。
> REST API の `/users` エンドポイントでよく使用されます。

---

## 2. `PolicyEnforcerConfig`

`PolicyEnforcerConfig` は **Keycloakの「保護されたリソース」や「ポリシー」をAPIクライアント側で適用するための設定** です。
主に **KeycloakのAuthorization Services（リソースベースのアクセス制御）** で使用されます。

### 主なフィールド

| フィールド             | 型       | 説明                                                |
| ----------------- | ------- | ------------------------------------------------- |
| `enforcementMode` | string  | ポリシー適用モード (`ENFORCING`, `PERMISSIVE`, `DISABLED`) |
| `clientId`        | string  | この設定を適用するクライアントID                                 |
| `policyEnforcer`  | object  | ポリシーエンフォーサの詳細設定（パスごとのルールなど）                       |
| `lazyLoadPaths`   | boolean | 保護パスを遅延ロードするかどうか                                  |
| `paths`           | array   | 個別のリソースパスごとのポリシー設定                                |

### 例

```json
{
  "clientId": "my-app",
  "enforcementMode": "ENFORCING",
  "paths": [
    {
      "path": "/admin/*",
      "methods": ["GET", "POST"],
      "scopes": ["admin"]
    }
  ]
}
```

> `PolicyEnforcerConfig` は、アプリケーションがKeycloakの保護リソースを利用する際に、どのルールでアクセス制御するかを定義する設定です。
> REST API というより、Keycloak Adapter（JavaScript, Java, Spring Bootなど）で読み込まれることが多いです。

---

## 3. 簡単な違いまとめ

| 項目           | UserRepresentation | PolicyEnforcerConfig     |
| ------------ | ------------------ | ------------------------ |
| 対象           | ユーザー               | クライアントのアクセス制御設定          |
| 主な用途         | ユーザー情報の取得・作成・更新・削除 | ポリシーエンフォーサーによるリソースアクセス制御 |
| REST APIで使うか | 直接多用（/users）       | Adapterや保護されたリソース設定で主に使用 |

---

### ポイント

* `UserRepresentation` = ユーザーの「データモデル」
* `PolicyEnforcerConfig` = アプリやAPIの「アクセス制御ルールの設定」