# Pythonでjsonlinesライブラリを使ってJSON Lines（JSONL）を読み込み・変換する

jsonlines は「抽出DSL」ではなく「JSONLファイルを安全に読む／書くためのライブラリ」**です。
そのため「抽出・変換」は **Pythonのコード側で行う**、という前提になります。

## 実現したいこと
Pythonのソースコード内で **jsonlinesライブラリ** を使用し、  
**JSON Lines（1行1JSON）形式のファイル**を読み込み、  
データを **抽出・条件絞り込み・変換** したい。

## 実行環境
- VS Code
- Python

---

## 1. jsonlinesとは？

- JSON Lines（別名 NDJSON）形式専用のPythonライブラリ
- **1行 = 1つのJSONオブジェクト**
- 大容量データ・ログ・ストリーミング処理に強い
- 配列JSON（`[{}, {}]`）ではなく **行指向**

---

## 2. 準備（インストール）

```bash
pip install jsonlines
````

---

## 3. JSON Lines（JSONL）の例

### data.jsonl

```json
{"id": 1, "name": "Alice", "age": 25}
{"id": 2, "name": "Bob", "age": 30}
{"id": 3, "name": "Charlie", "age": 35}
```

※ 改行ごとに独立したJSONオブジェクトになっています。

---

## 4. 基本の使い方（読み込み）

### ファイルをすべて読み込む

```python
import jsonlines

with jsonlines.open("data.jsonl") as reader:
    data = list(reader)

print(data)
```

**出力結果**

```python
[
    {'id': 1, 'name': 'Alice', 'age': 25},
    {'id': 2, 'name': 'Bob', 'age': 30},
    {'id': 3, 'name': 'Charlie', 'age': 35}
]
```

---

## 5. 抽出（Pythonで処理）

### 名前だけ抽出

```python
names = [row["name"] for row in data]
print(names)
```

**出力結果**

```text
['Alice', 'Bob', 'Charlie']
```

---

## 6. 条件付き抽出（フィルタ）

### 年齢が30以上のデータのみ

```python
filtered = [row for row in data if row["age"] >= 30]
print(filtered)
```

**出力結果**

```python
[
    {'id': 2, 'name': 'Bob', 'age': 30},
    {'id': 3, 'name': 'Charlie', 'age': 35}
]
```

---

## 7. 抽出＋変換（構造を作り直す）

### 必要な項目だけに変換

```python
converted = [
    {"user_id": row["id"], "username": row["name"]}
    for row in data
]

print(converted)
```

**出力結果**

```python
[
    {'user_id': 1, 'username': 'Alice'},
    {'user_id': 2, 'username': 'Bob'},
    {'user_id': 3, 'username': 'Charlie'}
]
```

---

## 8. 大容量ファイル向けの逐次処理 ⭐️

jsonlinesの最大の強みは **1行ずつ処理できること** です。

```python
import jsonlines

with jsonlines.open("data.jsonl") as reader:
    for row in reader:
        if row["age"] >= 30:
            print(row["name"])
```

* メモリに全件読み込まない
* ログ・ETL処理・バッチ向き

---

## 9. JSON Linesファイルへの書き込み

### 新しいJSONLファイルを作成

```python
import jsonlines

output_data = [
    {"id": 1, "name": "Tanaka"},
    {"id": 2, "name": "Suzuki"}
]

with jsonlines.open("output.jsonl", mode="w") as writer:
    for row in output_data:
        writer.write(row)
```

### output.jsonl の中身

```json
{"id": 1, "name": "Tanaka"}
{"id": 2, "name": "Suzuki"}
```

---

## 10. よく使うjsonlines APIまとめ

| やりたいこと   | 書き方                    |
| -------- | ---------------------- |
| ファイル読み込み | `jsonlines.open(path)` |
| 全件取得     | `list(reader)`         |
| 逐次処理     | `for row in reader:`   |
| 書き込み     | `writer.write(obj)`    |
| 上書き      | `mode="w"`             |
| 追記       | `mode="a"`             |

---

## 11. デバッグのコツ

* JSONの形式エラーは **行単位で例外が出る**
* どの行で失敗したか特定しやすい
* 巨大ファイルでも安全に処理可能

```python
try:
    with jsonlines.open("data.jsonl") as reader:
        for row in reader:
            pass
except Exception as e:
    print(e)
```

---

## まとめ

* jsonlinesは **JSON Lines専用ライブラリ**
* 抽出・変換はPythonコードで行う
* 大容量・ストリーミング処理に最適
* ログ、ETL、バッチ処理で真価を発揮