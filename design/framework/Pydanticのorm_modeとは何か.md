# orm_mode について

## 概要

`orm_mode` は **Pydantic** における設定オプションの一つで、  
**ORM（Object Relational Mapper）のオブジェクトを Pydantic モデルに直接変換できるようにする機能**です。

主に **FastAPI × SQLAlchemy** の構成で利用されます。

---

## なぜ orm_mode が必要か

Pydantic はデフォルトで **dict（辞書）形式**のデータを想定しています。

```python
{"id": 1, "name": "Alice"}
```

一方、ORM（例：SQLAlchemy）ではデータは **属性を持つオブジェクト**として扱われます。

```python
user.id
user.name
```

この形式の違いにより、ORMオブジェクトはそのままでは **Pydantic に解釈できません**。  
そこで `orm_mode = True` を設定します。

---

## 基本的な使い方

### Pydantic モデル定義

```python
from pydantic import BaseModel

class UserSchema(BaseModel):
    id: int
    name: str

    class Config:
        orm_mode = True
```

---

## orm_mode = True の効果

- 辞書形式だけでなく **属性アクセス（`.`）による値取得を許可**
- ORMオブジェクトを直接 Pydantic モデルへ変換可能
- FastAPI のレスポンスモデルとしてそのまま利用可能

---

## 使用例（FastAPI × SQLAlchemy）

### SQLAlchemy モデル

```python
class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True)
    name = Column(String)
```

### FastAPI エンドポイント

```python
@app.get("/users/{user_id}", response_model=UserSchema)
def get_user(user_id: int, db: Session = Depends(get_db)):
    user = db.query(User).get(user_id)
    return user
```

`orm_mode = True` が設定されているため、  
ORMオブジェクト（`user`）をそのまま返却できます。

---

## orm_mode が無い場合のエラー例

```text
value is not a valid dict
```

または

```text
TypeError: 'User' object is not subscriptable
```

ORMオブジェクトを辞書として扱おうとして失敗します。

---

## 内部的な挙動

### orm_mode = False（デフォルト）

- `obj["field"]` のような **辞書アクセスのみ許可**

### orm_mode = True

- `obj.field` のような **属性アクセスも許可**

---

## 主な利用シーン

- FastAPI の `response_model`
- SQLAlchemy / Django ORM / Tortoise ORM
- DBモデルから API レスポンスへの変換

---

## 注意点

- リクエストボディ（入力データ）には影響しない  
  ※ 出力（ORM → Pydantic）のみが対象
- ORM依存の設計になりやすいため責務分離に注意
- Pydantic v2 では設定方法が変更されている

---

## Pydantic v2 における変更点

Pydantic v2 では `orm_mode` は廃止され、  
以下の設定に置き換えられています。

```python
model_config = {
    "from_attributes": True
}
```

---

## まとめ

| 項目 | 内容 |
|------|------|
| 目的 | ORMオブジェクトをPydanticで扱う |
| 設定 | `Config.orm_mode = True` |
| 主用途 | FastAPIのレスポンスモデル |
| 対象 | 出力（レスポンス）専用 |
| v2対応 | `from_attributes=True` |