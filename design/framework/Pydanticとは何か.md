# Pydantic とは

**Pydantic** は、Python における **データバリデーション** と **設定管理** を行うためのライブラリです。  
Python の **型ヒント（type hints）** を活用して、安全で分かりやすいデータモデルを定義できます。

特に **FastAPI** との相性が非常に良いことで知られています。

---

## 主な特徴

### 1. 型ヒントベースのデータ検証
- `int`, `str`, `list` などの型ヒントをもとに自動検証
- 不正なデータは例外として検出

### 2. 自動型変換
- `"123"` → `int(123)` のように、可能な範囲で型変換を実施

### 3. シンプルなモデル定義
- クラス定義だけでデータ構造を表現可能
- 可読性が高く、保守しやすい

### 4. JSON / dict との相互変換
- API 開発で扱いやすい
- `dict()` や `json()` メソッドを提供

### 5. 高速
- 内部的に最適化されており、実用上十分高速

---

## 基本的な使い方

### インストール

```bash
pip install pydantic
```

### モデル定義例

```python
from pydantic import BaseModel

class User(BaseModel):
    id: int
    name: str
    age: int | None = None
```

### データの検証と生成

```python
user = User(id="1", name="Taro", age="20")

print(user)
# id=1 name='Taro' age=20

```

### 不正なデータの場合

```python
User(id="abc", name="Taro")
```

## Pydantic v2 について（補足）

- Pydantic v2 では内部実装が刷新され、**大幅に高速化**されています
- コア部分が Rust ベース（pydantic-core）になり、パフォーマンスが向上
- 従来の `@validator` デコレーターは非推奨となり、  
  `@field_validator` が推奨されています
- バリデーションの実行タイミングや挙動が、より明確で厳密になりました
- 設定管理用の `BaseSettings` は  
  **`pydantic-settings` パッケージ** に分離されました

```python
# v2 でのバリデーション例
from pydantic import BaseModel, field_validator

class User(BaseModel):
    age: int

    @field_validator("age")
    @classmethod
    def check_age(cls, v):
        if v < 0:
            raise ValueError("age must be positive")
        return v
```

## まとめ

- **Pydantic** は Python の型ヒントを活用したデータバリデーションライブラリ
- モデル定義だけで、**検証・型変換・エラーハンドリング**を自動化できる
- Web API（特に FastAPI）や設定管理、外部データの検証に最適
- **Pydantic v2** では Rust ベースの実装により、より高速で堅牢になった
- 型安全なコードを書くことで、**バグの早期発見**と**保守性向上**に貢献する

Pydantic を使うことで、Python アプリケーションの信頼性と開発効率を大きく向上させることができます。
