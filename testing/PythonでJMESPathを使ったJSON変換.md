# PythonでJMESPathを使ってJSONを抽出・変換する

## 実現したいこと
Pythonのソースコード内で **JMESPath** を使用し、  
JSONデータを **抽出・条件絞り込み・変換** したい。

## 実行環境
- VS Code
- Python

---

## 1. 準備（インストール）

まずは jmespath をインストールします。

```bash
pip install jmespath
````

VS Code のターミナルから実行すればOKです。

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

### JMESPathで抽出

```python
import jmespath

result = jmespath.search("users[].name", data)
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
expr = "users[?age >= `30`].name"
result = jmespath.search(expr, data)
print(result)
```

**出力結果**

```text
['Bob', 'Charlie']
```

※ 数値はバッククォート `` ` `` で囲むのがJMESPathの仕様です。

---

## 4. 抽出＋変換（構造を作り直す）

### 必要な形にJSONを変換

```python
expr = "users[].{user_id: id, username: name}"
result = jmespath.search(expr, data)
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

JMESPathは「JSON → JSON」の変換を1行で書けるのが大きな強みです。

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
jmespath.search("order.items[].name", data)
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

### PythonでJSONファイルを読み込む

```python
import json
import jmespath

# JSONファイル読み込み
with open("data.json", "r", encoding="utf-8") as f:
    data = json.load(f)
```

---

### JMESPathでデータ抽出

#### 社員名一覧を取得

```python
result = jmespath.search("employees[].name", data)
print(result)
```

**出力結果**

```text
['Tanaka', 'Suzuki']
```

---

#### Engineering部門のみ抽出

```python
expr = "employees[?department == 'Engineering']"
result = jmespath.search(expr, data)
print(result)
```

---

#### 必要な項目だけに変換

```python
expr = "employees[].{id: id, name: name, dept: department}"
result = jmespath.search(expr, data)
print(result)
```

**出力結果**

```python
[
    {'id': 1, 'name': 'Tanaka', 'dept': 'Sales'},
    {'id': 2, 'name': 'Suzuki', 'dept': 'Engineering'}
]
```

---

## 7. よく使うJMESPath構文まとめ

| やりたいこと    | 式                             |
| --------- | ----------------------------- |
| 配列の全要素    | `items[]`                     |
| 条件フィルタ    | `items[?price > \`50`]`       |
| 特定キー取得    | `items[].name`                |
| オブジェクト再構成 | `items[].{n: name, p: price}` |
| 先頭要素取得    | `items[0]`                    |

---

## 8. デバッグのコツ

JMESPathは式が間違っていると `None` が返ることがあります。

```python
result = jmespath.search(expr, data)
if result is None:
    print("JMESPath式が間違っている可能性があります")
```

また、公式の **JMESPath Playground** にJSONを貼り付けて
クエリを試すとデバッグがとても楽になります。

---

## まとめ

* JMESPathはJSONの抽出・変換に非常に強力
* Pythonでは `jmespath.search()` だけで利用可能
* JSONファイルやAPIレスポンス加工に最適
* 「JSON → JSON」変換をシンプルに書ける
