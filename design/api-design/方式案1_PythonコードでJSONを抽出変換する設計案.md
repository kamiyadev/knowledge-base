## プロジェクト構成例

```
project/
├── config.yaml        # 抽出ルールを定義
├── data.json          # JSONデータ
└── extract.py         # 実装（Python）
```

---

## 1. 設定ファイル（YAML）

### config.yaml

```yaml
input:
  path: data.json        # 入力JSONファイル

extract:
  query: "users[?age >= `30`].{id: id, name: name}"  # JMESPath式

output:
  print: true             # Trueなら標準出力に出力
  save_path: null         # 保存したい場合はファイルパス
```

**ポイント**

* 「どこを取得するか」は **JMESPath文字列**で定義
* Pythonコードは **ファイル読み込み・抽出・出力**のみ担当

---

## 2. JSONデータ例（data.json）

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

## 3. Python実装（extract.py）

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
    # コマンドラインで設定ファイルを指定可能
    config_path = sys.argv[1] if len(sys.argv) > 1 else "config.yaml"
    config = load_config(config_path)

    input_path = Path(config["input"]["path"])
    query = config["extract"]["query"]

    # JSON読み込み
    data = load_json(input_path)

    # JMESPathで抽出
    result = jmespath.search(query, data)

    # 標準出力
    if config["output"].get("print", False):
        print(json.dumps(result, ensure_ascii=False, indent=2))

    # ファイル保存
    save_path = config["output"].get("save_path")
    if save_path:
        save_json(result, Path(save_path))

if __name__ == "__main__":
    main()
```

---

## 4. 実行方法

```bash
python extract.py              # デフォルト config.yaml を使用
python extract.py myconfig.yaml  # 任意の設定ファイル指定
```

---

## 5. 実行例（標準出力）

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

## 6. 設計ポイント

* **抽出ルールをYAMLに分離** → コード変更不要で条件変更可能
* **JMESPathで宣言的抽出** → JSON構造変更にも柔軟
* **Pythonは実行エンジンに徹する** → 再利用性・保守性が高い
* **標準出力 or ファイル保存** が簡単に切り替え可能

---

💡 この設計により、**「文字列でどこを取得するかを設定 → コマンド実行で抽出」** が簡単に実現できます。