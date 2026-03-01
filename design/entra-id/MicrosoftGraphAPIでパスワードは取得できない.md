# Microsoft Graph API でパスワード取得可否について

## 結論
**Microsoft Graph API を使って、ユーザーの実際のパスワード（平文の文字列）を取得することは不可。**  
セキュリティ上の理由から、**仕様として明確に禁止**されている。

---

## 理由
- Microsoft Graph API には `passwordAuthenticationMethod` というパスワード関連の API が存在する
- しかし、この API を呼び出しても **パスワード値（password プロパティ）は常に `null`**
- 実際のパスワード文字列は **一切返却されない**

これは Microsoft の公式ドキュメントにも明記されている。

---

## 公式ドキュメント（取得不可であることが記載されている記事）

- **passwordAuthenticationMethod を取得する**
  - https://learn.microsoft.com/ja-jp/graph/api/passwordauthenticationmethod-get?view=graph-rest-1.0  
---

## Graph API で「できること／できないこと」

| 操作内容 | 可否 | 補足 |
|--------|------|------|
| ユーザーのパスワードを取得 | ❌ 不可 | 平文パスワードは取得できない |
| ユーザーのパスワードを変更／リセット | ✅ 可能 | `passwordProfile` や変更用 API を使用 |
| 最終パスワード変更日時の取得 | ✅ 可能 | `lastPasswordChangeDateTime` などで確認可能 |

---

## まとめ
- Microsoft Graph API では **パスワード取得は不可能**
- 公式ドキュメントにも明確に記載されている
- 可能なのは **パスワードの変更・リセットや状態情報の取得のみ**
