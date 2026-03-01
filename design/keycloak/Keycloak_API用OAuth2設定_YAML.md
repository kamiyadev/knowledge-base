以下は、**Keycloak向け・API用（Client Credentials フロー）**の定義情報を
**Markdown形式**で整理したものです。

---

# Keycloak OAuth 2.0 設定（API用 / Client Credentials）

## 概要

* 認証基盤：**Keycloak**
* 利用用途：**Machine to Machine（API）**
* OAuth 2.0 フロー：**Client Credentials**
* ユーザー認証なし（Service Account を使用）

---

## OAuth 2.0 定義（YAML）

```yaml
oauth2:
  provider:
    type: keycloak
    realm: example-realm
    base_url: https://keycloak.example.com
    issuer: https://keycloak.example.com/realms/example-realm

  client:
    client_id: example-api-client
    client_secret: your-client-secret
    access_type: confidential
    service_accounts_enabled: true
    grant_types:
      - client_credentials
    scopes:
      - api.read
      - api.write

  endpoints:
    token_endpoint: https://keycloak.example.com/realms/example-realm/protocol/openid-connect/token
    introspection_endpoint: https://keycloak.example.com/realms/example-realm/protocol/openid-connect/token/introspect
    revocation_endpoint: https://keycloak.example.com/realms/example-realm/protocol/openid-connect/revoke
    jwks_uri: https://keycloak.example.com/realms/example-realm/protocol/openid-connect/certs

  tokens:
    access_token:
      type: Bearer
      expires_in: 300
      signing_alg: RS256
      audience: example-api

  security:
    tls_required: true
    m2m: true

  authorization:
    roles:
      realm_roles:
        - api-user
      client_roles:
        example-api:
          - read
          - write
    scope_role_mapping:
      api.read:
        - read
      api.write:
        - write

  metadata:
    description: Keycloak OAuth2 configuration for API (Client Credentials)
    created_by: security-team
    version: "1.0"
```

---

## Keycloak 管理画面との対応

| YAML項目                      | Keycloak 管理画面                      |
| --------------------------- | ---------------------------------- |
| `access_type: confidential` | Clients → Access Type              |
| `service_accounts_enabled`  | Clients → Service Accounts Enabled |
| `client_credentials`        | Clients → Capability config        |
| `client_roles`              | Clients → Roles                    |
| `realm_roles`               | Realm → Roles                      |
| `jwks_uri`                  | Realm → Keys                       |

---

## API側の実装前提

* HTTPヘッダ

  ```http
  Authorization: Bearer <access_token>
  ```

* トークン検証方法

  * **JWKS（推奨）**：署名検証（高速）
  * **Introspection**：トークン状態確認（失効対応）

* ユーザーコンテキスト

  * なし（Service Account のみ）

---

## セキュリティ上の推奨事項

* `client_secret` は **Vault / Secret Manager** で管理
* HTTPS（TLS）必須
* スコープとロールは **最小権限**で設計
* 有効期限は短め（例：300秒）

---

## 参考：取得トークン例（curl）

```bash
curl -X POST \
  https://keycloak.example.com/realms/example-realm/protocol/openid-connect/token \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials" \
  -d "client_id=example-api-client" \
  -d "client_secret=your-client-secret"
```