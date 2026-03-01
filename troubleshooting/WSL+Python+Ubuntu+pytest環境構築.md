# WSL + Python 環境構築 & pyjq 導入まとめ

## 1. Ubuntu（WSL）のバージョン確認
```bash
lsb_release -a
````

簡易確認：

```bash
lsb_release -r
```

ファイル確認（CI向け）：

```bash
cat /etc/os-release
```

---

## 2. WSL に pip をインストールする

### Python が入っているか確認

```bash
python3 --version
```

### pip をインストール

```bash
sudo apt update
sudo apt install -y python3-pip python3-venv
```

確認：

```bash
python3 -m pip --version
```

---

## 3. pip / pip3 の考え方（重要）

### 原則

* グローバル環境
  👉 `python3 -m pip`
* 仮想環境（venv）内
  👉 `pip` を使ってOK

### 理由

* `pip` がどの Python に紐づくか分からない事故を防ぐため

---

## 4. pyjq インストール前の必須依存関係

pyjq は **C拡張 + jq + autotools** が必要。

### 必須パッケージ

```bash
sudo apt update
sudo apt install -y \
  jq \
  libjq-dev \
  python3-dev \
  build-essential \
  autoconf \
  automake \
  libtool \
  pkg-config
```

※ `autoreconf` エラーは `autoconf / automake / libtool` 不足が原因

---

## 5. 仮想環境（推奨）

```bash
python3 -m venv venv
source venv/bin/activate
```

pip を最新化：

```bash
pip install --upgrade pip setuptools wheel
```

---

## 6. pyjq インストール

```bash
pip install pyjq
```

---

## 7. インストール確認

```bash
python - << 'EOF'
import pyjq
data = {"items": [{"id": 1}, {"id": 2}]}
print(pyjq.all(".items[].id", data))
EOF
```

期待結果：

```text
[1, 2]
```

---

## 8. pyjq のバージョン確認方法

### 一番確実（pip）

```bash
python3 -m pip show pyjq
```

### Python から（推奨）

```bash
python3 -c "from importlib.metadata import version; print(version('pyjq'))"
```

### **version** があるか確認

```bash
python3 -c "import pyjq; print(getattr(pyjq, '__version__', 'no __version__'))"
```

---

## 9. よくあるエラーと原因

| エラー                        | 原因               | 対処                             |
| -------------------------- | ---------------- | ------------------------------ |
| `autoreconf: No such file` | autotools不足      | `autoconf automake libtool`    |
| `jq.h not found`           | libjq-dev不足      | `sudo apt install libjq-dev`   |
| `Python.h not found`       | python3-dev不足    | `sudo apt install python3-dev` |
| ビルド失敗                      | Windows Python混入 | venv使用・which確認                 |

---

## 10. 確認コマンド（混線チェック）

```bash
which python
which python3
which pip
which pip3
pip -V
```

理想：

* `/usr/bin/python3`
* Linux側の site-packages

---

## 11. 次に繋がるテーマ

* pyjq × pytest（JSON変換テスト）
* FastAPI × pyjq
* C拡張ライブラリのビルドログの読み方
* WSL ビルド環境トラブルシュート手順
