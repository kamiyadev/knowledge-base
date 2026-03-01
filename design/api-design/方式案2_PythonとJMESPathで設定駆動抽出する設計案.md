## 1. プロジェクト構成例

```
project/
├── config.yaml        # 抽出ルールを定義
├── data.json          # データファイル
└── extract.py         # 実装ファイル（Python）
```

---

## 2. 設定ファイル（YAML）

### config.yaml

```yaml
input:
  path: data.json        # 入力JSONファイル
  format: json           # JSONのみ

extract:
  query: "users[?age >= `30`].{id: id, name: name}"  # JMESPath式

output:
  print: true             # Trueなら標準出力に出力
  save_path: null         # 保存したい場合はファイルパス
```

ポイント：

* **どこを取得するか** は JMESPath の文字列だけで指定
* コード側は **ファイル形式を判定して処理するだけ**

---

## 3. データファイル例（data.json）

```json
{
  "users": [
    { "id": 1, "name": "Alice", "age": 25 },
    { "id": 2, "name": "Bob", "age": 30 },
    { "id": 3, "name": "Charlie", "age": 35 }
  ]
}
```

---

## 4. Python実装（extract.py）

```python
import json
import yaml
import jmespath
from pathlib import Path
import sys

def load_config(path: str) -> dict:
    with open(path, "r", encoding="utf-8") as f:
        return yaml.safe_load(f)

def load_json(path: Path):
    with open(path, "r", encoding="utf-8") as f:
        return json.load(f)

def save_json(data, path: Path):
    with open(path, "w", encoding="utf-8") as f:
        json.dump(data, f, ensure_ascii=False, indent=2)

def main():
    # コマンドライン引数で設定ファイル指定可能
    config_path = sys.argv[1] if len(sys.argv) > 1 else "config.yaml"
    config = load_config(config_path)

    input_path = Path(config["input"]["path"])
    query = config["extract"]["query"]

    # JSONファイルを読み込む
    data = load_json(input_path)

    # JMESPathで抽出
    result = jmespath.search(query, data)

    # 標準出力
    if config["output"].get("print", False):
        print(json.dumps(result, ensure_ascii=False, indent=2))

    # ファイルに保存
    save_path = config["output"].get("save_path")
    if save_path:
        save_json(result, Path(save_path))

if __name__ == "__main__":
    main()
```

---

## 5. 実行方法

```bash
python extract.py              # デフォルト config.yaml を使用
python extract.py myconfig.yaml  # 任意の設定ファイルを指定
```

---

## 6. 実行例

```json
[
  {
    "id": 2,
    "name": "Bob"
  },
  {
    "id": 3,
    "name": "Charlie"
  }
]
```

---

## 7. 設計のポイント

* **抽出ルールを設定ファイルに外出し**

  * コード変更なしで抽出条件を変更可能
* **JMESPathで宣言的に抽出**

  * JSON構造変更にも柔軟
* **Pythonスクリプトは実行エンジンに徹する**

  * 再利用性・保守性が高い
* **オプションで出力先を変更可能**

  * 標準出力 or ファイル保存

---

## 8. 拡張例

* 複数クエリ対応（配列で設定）
* 出力をNDJSONに保存
* JMESPath構文エラーの事前チェック
* JSONストリーミング処理（大容量対応）

---

この構成で、**「設定ファイルでどこを取得するかを定義 → コマンド実行で抽出」** が簡単に実現できます。

---

💡 ポイント：

* JMESPathでの抽出式変更 = 設定変更のみ
* 実装コードは固定化
* 上司やチームに提示しやすく、再利用性が高い設計です。
