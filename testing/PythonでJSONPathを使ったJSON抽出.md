# PythonでJSONPathを使ってJSONを抽出・変換する

## 実現したいこと
Pythonのソースコード内で **JSONPath** を使用し、  
JSONデータを **抽出・条件絞り込み・簡易変換** したい。

## 実行環境
- VS Code
- Python

---

## 1. 準備（インストール）

Pythonでは `jsonpath-ng` を使うのが一般的です。

```bash
pip install jsonpath-ng
````

---

## 2. 基本の使い方（抽出）

### サンプルJSON

```python
data = {
    "users": [
        {"id": 1, "name": "Alice", "age": 25},
        {"id": 2, "name": "Bob", "age": 30},
        {"id": 3, "name": "Charlie", "age": 35}
    ]
}
```

### JSONPathで抽出

```python
from jsonpath_ng import parse

expr = parse("$.users[*].name")
result = [match.value for match in expr.find(data)]
print(result)
```

**出力結果**

```text
['Alice', 'Bob', 'Charlie']
```

---

## 3. 条件付き抽出（フィルタ）

### 年齢が30以上のユーザーだけ抽出

```python
expr = parse("$.users[?(@.age >= 30)].name")
result = [match.value for match in expr.find(data)]
print(result)
```

**出力結果**

```text
['Bob', 'Charlie']
```

---

## 4. 抽出結果の整形（Python側で変換）

※ JSONPathは **JMESPathのような構造再生成が弱いため**、
取得後にPythonで整形するのが一般的です。

```python
expr = parse("$.users[*]")
users = [match.value for match in expr.find(data)]

result = [
    {"user_id": u["id"], "username": u["name"]}
    for u in users
]

print(result)
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

## 5. ネストしたJSONの例

### JSON

```python
data = {
    "order": {
        "items": [
            {"name": "apple", "price": 100},
            {"name": "banana", "price": 80}
        ]
    }
}
```

### 商品名だけ抽出

```python
expr = parse("$.order.items[*].name")
result = [match.value for match in expr.find(data)]
print(result)
```

**出力結果**

```text
['apple', 'banana']
```

---

## 6. ファイル（JSON）読み込みのサンプル ⭐️

### JSONファイル例（data.json）

```json
{
  "employees": [
    {
      "id": 1,
      "name": "Tanaka",
      "department": "Sales",
      "salary": 500000
    },
    {
      "id": 2,
      "name": "Suzuki",
      "department": "Engineering",
      "salary": 700000
    }
  ]
}
```

---

### PythonでJSONファイルを読み込む

```python
import json
from jsonpath_ng import parse

with open("data.json", "r", encoding="utf-8") as f:
    data = json.load(f)
```

---

### JSONPathでデータ抽出

#### 社員名一覧を取得

```python
expr = parse("$.employees[*].name")
result = [match.value for match in expr.find(data)]
print(result)
```

**出力結果**

```text
['Tanaka', 'Suzuki']
```

---

#### Engineering部門のみ抽出

```python
expr = parse("$.employees[?(@.department == 'Engineering')]")
result = [match.value for match in expr.find(data)]
print(result)
```

---

#### 必要な項目だけに変換（Python側で整形）

```python
expr = parse("$.employees[*]")
employees = [match.value for match in expr.find(data)]

result = [
    {
        "id": e["id"],
        "name": e["name"],
        "dept": e["department"]
    }
    for e in employees
]

print(result)
```

---

## 7. よく使うJSONPath構文まとめ

| やりたいこと   | 式                   |
| -------- | ------------------- |
| ルート      | `$`                 |
| 配列の全要素   | `[*]`               |
| 条件フィルタ   | `[?(@.price > 50)]` |
| 特定キー取得   | `$.items[*].name`   |
| 再帰検索     | `$..name`           |
| 配列インデックス | `[0]`               |

---

## 8. デバッグのコツ

* `expr.find()` は **Matchオブジェクトの配列**を返す
* 実際の値は `match.value` で取得する
* 0件の場合は空配列 `[]` になる（Noneではない）

```python
matches = expr.find(data)
if not matches:
    print("条件に一致するデータがありません")
```

---

## まとめ

* JSONPathは **抽出特化**
* 複雑な変換はPython側で処理するのが前提
* JMESPathより歴史が長く、ツール対応が多い
* 「どこにあるか探す」用途に強い