## 📌 `user` リソースのプロパティ一覧（No・例付き）

| No | プロパティ名                          | 型                                      | 説明                    | 例                                                  |
| -: | ------------------------------- | -------------------------------------- | --------------------- | -------------------------------------------------- |
|  1 | aboutMe                         | String                                 | ユーザーが自分自身についての自由テキスト。 | `"クラウドエンジニアです"`                                    |
|  2 | accountEnabled                  | Boolean                                | アカウントが有効かどうか。         | `true`                                             |
|  3 | ageGroup                        | ageGroup                               | ユーザーの年齢グループ。          | `"adult"`                                          |
|  4 | assignedLicenses                | assignedLicense collection             | ユーザーに割り当てられたライセンス。    | `[ { "skuId": "…" } ]`                             |
|  5 | assignedPlans                   | assignedPlan collection                | 割り当てられたプラン（読み取り専用）。   | `[ { "service": "Exchange" } ]`                    |
|  6 | birthday                        | DateTimeOffset                         | 誕生日。                  | `"1990-04-01"`                                     |
|  7 | businessPhones                  | String collection                      | 勤務先電話番号。              | `["+81-3-1234-5678"]`                              |
|  8 | city                            | String                                 | 市区町村。                 | `"Tokyo"`                                          |
|  9 | companyName                     | String                                 | 会社名。                  | `"Contoso Ltd."`                                   |
| 10 | consentProvidedForMinor         | consentProvidedForMinor                | 未成年者に対する同意の有無。        | `"granted"`                                        |
| 11 | country                         | String                                 | 国/地域。                 | `"JP"`                                             |
| 12 | createdDateTime                 | DateTimeOffset                         | 作成日時（読み取り専用）。         | `"2023-01-10T09:00:00Z"`                           |
| 13 | creationType                    | String                                 | ユーザーの作成方法。            | `"Invitation"`                                     |
| 14 | customSecurityAttributes        | customSecurityAttributeValue           | カスタム セキュリティ属性。        | `{ "Project": "A" }`                               |
| 15 | deletedDateTime                 | DateTimeOffset                         | 削除日時。                 | `"2024-03-01T00:00:00Z"`                           |
| 16 | department                      | String                                 | 部署名。                  | `"IT部"`                                            |
| 17 | displayName                     | String                                 | 表示名。                  | `"Taro Yamada"`                                    |
| 18 | employeeHireDate                | DateTimeOffset                         | 入社日。                  | `"2018-04-01"`                                     |
| 19 | employeeLeaveDateTime           | DateTimeOffset                         | 退職日。                  | `"2025-03-31"`                                     |
| 20 | employeeId                      | String                                 | 従業員 ID。               | `"E12345"`                                         |
| 21 | employeeOrgData                 | employeeOrgData                        | 組織情報。                 | `{ "costCenter": "CC01" }`                         |
| 22 | employeeType                    | String                                 | 従業員の種別。               | `"FullTime"`                                       |
| 23 | externalUserState               | String                                 | 外部ユーザーの状態。            | `"Accepted"`                                       |
| 24 | externalUserStateChangeDateTime | DateTimeOffset                         | 外部ユーザー状態の変更日時。        | `"2023-06-01T12:00:00Z"`                           |
| 25 | faxNumber                       | String                                 | FAX 番号。               | `"+81-3-1234-9999"`                                |
| 26 | givenName                       | String                                 | 名。                    | `"Taro"`                                           |
| 27 | hireDate                        | DateTimeOffset                         | 採用日（SharePoint）。      | `"2018-04-01"`                                     |
| 28 | id                              | String                                 | ユーザーの一意 ID。           | `"a1b2c3d4-e5f6"`                                  |
| 29 | identities                      | objectIdentity collection              | サインイン ID 情報。          | `[ { "signInType": "emailAddress" } ]`             |
| 30 | imAddresses                     | String collection                      | IM アドレス。              | `["sip:taro@contoso.com"]`                         |
| 31 | interests                       | String collection                      | 興味・関心。                | `["AI", "Cloud"]`                                  |
| 32 | isManagementRestricted          | Boolean                                | 管理制限ユーザーかどうか。         | `false`                                            |
| 33 | isResourceAccount               | Boolean                                | リソース アカウントかどうか。       | `false`                                            |
| 34 | jobTitle                        | String                                 | 役職名。                  | `"Senior Engineer"`                                |
| 35 | lastPasswordChangeDateTime      | DateTimeOffset                         | 最終パスワード変更日時。          | `"2024-11-01T10:00:00Z"`                           |
| 36 | legalAgeGroupClassification     | legalAgeGroupClassification            | 法的年齢区分。               | `"adult"`                                          |
| 37 | licenseAssignmentStates         | licenseAssignmentState collection      | ライセンス割り当て状態。          | `[ { "state": "Active" } ]`                        |
| 38 | mail                            | String                                 | メールアドレス。              | `"taro@contoso.com"`                               |
| 39 | mailboxSettings                 | mailboxSettings                        | メールボックス設定。            | `{ "timeZone": "Tokyo Standard Time" }`            |
| 40 | mailNickname                    | String                                 | メール ニックネーム。           | `"taro"`                                           |
| 41 | mobilePhone                     | String                                 | 携帯電話番号。               | `"+81-90-1234-5678"`                               |
| 42 | mySite                          | String                                 | My Site の URL。        | `"https://contoso-my.sharepoint.com"`              |
| 43 | officeLocation                  | String                                 | オフィス所在地。              | `"Tokyo HQ"`                                       |
| 44 | onPremisesDistinguishedName     | String                                 | オンプレミス DN。            | `"CN=Taro,OU=Users,DC=contoso,DC=com"`             |
| 45 | onPremisesDomainName            | String                                 | オンプレミス ドメイン名。         | `"contoso.local"`                                  |
| 46 | onPremisesExtensionAttributes   | onPremisesExtensionAttributes          | 拡張属性。                 | `{ "extensionAttribute1": "A" }`                   |
| 47 | onPremisesImmutableId           | String                                 | オンプレミス Immutable ID。  | `"QkM0NTY="`                                       |
| 48 | onPremisesLastSyncDateTime      | DateTimeOffset                         | 最終同期日時。               | `"2024-12-01T01:00:00Z"`                           |
| 49 | onPremisesProvisioningErrors    | onPremisesProvisioningError collection | プロビジョニング エラー。         | `[ { "errorCode": "DirectoryError" } ]`            |
| 50 | onPremisesSamAccountName        | String                                 | SAM アカウント名。           | `"taro.y"`                                         |
| 51 | onPremisesSecurityIdentifier    | String                                 | オンプレミス SID。           | `"S-1-5-21-..."`                                   |
| 52 | onPremisesSyncEnabled           | Boolean                                | 同期有効フラグ。              | `true`                                             |
| 53 | onPremisesUserPrincipalName     | String                                 | オンプレミス UPN。           | `"taro@contoso.local"`                             |
| 54 | otherMails                      | String collection                      | その他のメールアドレス。          | `["taro.alt@contoso.com"]`                         |
| 55 | passwordPolicies                | String                                 | パスワード ポリシー。           | `"DisablePasswordExpiration"`                      |
| 56 | passwordProfile                 | passwordProfile                        | パスワード プロファイル。         | `{ "forceChangePasswordNextSignIn": true }`        |
| 57 | pastProjects                    | String collection                      | 過去のプロジェクト。            | `["Project A"]`                                    |
| 58 | postalCode                      | String                                 | 郵便番号。                 | `"100-0001"`                                       |
| 59 | preferredDataLocation           | String                                 | 優先データ ロケーション。         | `"JPN"`                                            |
| 60 | preferredLanguage               | String                                 | 優先言語。                 | `"ja-JP"`                                          |
| 61 | preferredName                   | String                                 | 優先表示名。                | `"Taro"`                                           |
| 62 | provisionedPlans                | ProvisionedPlan collection             | プロビジョン済みプラン。          | `[ { "service": "SharePoint" } ]`                  |
| 63 | proxyAddresses                  | String collection                      | プロキシ アドレス。            | `["SMTP:taro@contoso.com"]`                        |
| 64 | refreshTokensValidFromDateTime  | DateTimeOffset                         | リフレッシュ トークン有効日時。      | `"2024-01-01T00:00:00Z"`                           |
| 65 | responsibilities                | String collection                      | 責任範囲。                 | `["System Admin"]`                                 |
| 66 | serviceProvisioningErrors       | serviceProvisioningError collection    | サービス エラー。             | `[ { "service": "Exchange" } ]`                    |
| 67 | schools                         | String collection                      | 学校情報。                 | `["Tokyo University"]`                             |
| 68 | securityIdentifier              | String                                 | セキュリティ識別子 (SID)。      | `"S-1-12-1-..."`                                   |
| 69 | showInAddressList               | Boolean                                | アドレス帳に表示するか。          | `true`                                             |
| 70 | signInActivity                  | signInActivity                         | サインイン アクティビティ。        | `{ "lastSignInDateTime": "2025-01-01T08:00:00Z" }` |
| 71 | signInSessionsValidFromDateTime | DateTimeOffset                         | セッション無効化日時。           | `"2024-10-01T00:00:00Z"`                           |
| 72 | skills                          | String collection                      | スキル。                  | `["Azure", "C#"]`                                  |
| 73 | state                           | String                                 | 都道府県。                 | `"Tokyo"`                                          |
| 74 | streetAddress                   | String                                 | 住所。                   | `"1-1-1 Chiyoda"`                                  |
| 75 | surname                         | String                                 | 姓。                    | `"Yamada"`                                         |
| 76 | usageLocation                   | String                                 | 利用場所（国コード）。           | `"JP"`                                             |
| 77 | userPrincipalName               | String                                 | ユーザー プリンシパル名 (UPN)。   | `"taro@contoso.onmicrosoft.com"`                   |
| 78 | userType                        | String                                 | ユーザー種別。               | `"Member"`                                         |
