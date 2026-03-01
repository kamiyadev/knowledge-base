# JMESPath / JSONPath / jsonlines 比較表（Python）

## 位置づけの違い

| 項目 | JMESPath | JSONPath | jsonlines |
|---|---|---|---|
| 分類 | JSONクエリ言語 | JSONクエリ言語 | JSONL入出力ライブラリ |
| 主目的 | 抽出＋変換 | 抽出 | 読み込み／書き込み |
| DSLの有無 | ◎ | ◎ | ❌ |
| 対象データ | JSON | JSON | JSON Lines（JSONL / NDJSON） |

---

## 機能比較

| 観点 | JMESPath | JSONPath | jsonlines |
|---|---|---|---|
| JSON抽出 | ◎ | ◎ | △（Pythonで実装） |
| 条件フィルタ | ◎ | ◎ | △ |
| 構造変換（JSON→JSON） | ◎ | ❌ | ❌ |
| 再帰検索 | △ | ◎ | ❌ |
| 大容量データ対応 | △ | △ | ◎ |
| ストリーミング処理 | ❌ | ❌ | ◎ |
| Python依存度 | 低 | 中 | 高 |
| 学習コスト | 中 | 低 | 低 |

---

## 記述スタイルの違い

| 観点 | JMESPath | JSONPath | jsonlines |
|---|---|---|---|
| 記述場所 | クエリ文字列 | クエリ文字列 | Pythonコード |
| 可読性 | ◎（慣れると高い） | ○ | ○ |
| 1行完結度 | ◎ | ○ | ❌ |
| Python後処理 | ほぼ不要 | 必須になりがち | 必須 |

---

## 代表的な用途

| ユースケース | 向いているもの | 理由 |
|---|---|---|
| APIレスポンス加工 | JMESPath | 抽出＋変換を1行で書ける |
| 設定ファイル参照 | JSONPath | 深いネスト探索が楽 |
| ログ処理 | jsonlines | 逐次処理・大容量向き |
| ETL処理 | jsonlines + JMESPath | 読み込み＋変換の分業 |
| 単発抽出スクリプト | JSONPath | 書きやすい |
| JSON→JSON変換 | JMESPath | DSLで完結 |

---

## 同じ処理をした場合の違い（例）

### 「30歳以上のユーザーの名前を取得」

| 手法 | 記述例 | 特徴 |
|---|---|---|
| JMESPath | `users[?age >= \`30\`].name` | 1行・完結 |
| JSONPath | `$.users[?(@.age >= 30)].name` | 抽出のみ |
| jsonlines | `if row["age"] >= 30:` | Python制御 |

---

## 保守・運用観点

| 観点 | JMESPath | JSONPath | jsonlines |
|---|---|---|---|
| 学習コスト | 中 | 低 | 低 |
| 可読性 | ○ | ○ | ◎ |
| 属人化リスク | 中 | 低 | 低 |
| 他言語展開 | ◎ | ◎ | △ |
| 長期運用 | ○ | ○ | ◎ |

---

## 実務での使い分け指針（判断基準）

| 状況 | 選択理由 | 採用技術 |
|---|---|---|
| API結果を整形して次工程へ渡す | コードが簡潔 | JMESPath |
| JSON構造が深く、探索が必要 | 再帰検索が可能 | JSONPath |
| 数十万行以上のデータ処理 | メモリ効率が高い | jsonlines |
| ETL・バッチ処理 | 役割分離が可能 | jsonlines + JMESPath |

---

## よくある誤解

| 誤解 | 実際 |
|---|---|
| どれか1つに統一すべき | **用途が違うため併用が正解** |
| jsonlinesはJSON操作ができる | **入出力専用** |
| JSONPathとJMESPathは同じ | **変換可否が決定的に違う** |

---

## まとめ

| 判断軸 | 結論 |
|---|---|
| 単純抽出 | JSONPath |
| 加工・変換 | JMESPath |
| 大量・長期運用 | jsonlines |
| 実務最適解 | **組み合わせ利用** |