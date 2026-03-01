了解です 👍
ここまでのやり取りを **「何が起きたか → なぜダメだったか → どうすればいいか」** の流れでまとめます。

---

# これまでのまとめ（WSL / Ubuntu / pyjq）

## 1️⃣ 目的

WSL の Ubuntu 環境で **pyjq をインストールして使いたい**

---

## 2️⃣ 最初にやろうとしたこと

```bash
pip3 install pyjq
```

### 出たエラー

```text
error: externally-managed-environment
```

### 理由

* Ubuntu の Python は **OS 管理（PEP 668）**
* system Python に `pip install` するのは禁止
* → **venv（仮想環境）が必須**

---

## 3️⃣ venv を作ろうとしたが失敗

```bash
python3 -m venv venv
```

### 出たエラー

```text
ensurepip returned non-zero exit status 1
venv/bin/python3: No such file or directory
venv/bin/activate: No such file or directory
```

### 理由

* `python3-venv` / `python3-full` が未整備
* `ensurepip` が失敗
* **壊れた venv が残り続けていた**
* `.bashrc` で自動 activate しようとしてさらに混乱

👉 **venv は一度も正しく作成できていなかった**

---

## 4️⃣ venv 問題を整理して判明したこと

* system の `python3`（`/usr/bin/python3`）は正常
* venv は **未完成・壊れた状態**
* `pip` は常に **system pip** を指していた
* だから何度やっても **PEP 668 エラーが再発**

---

## 5️⃣ 決定的な原因

### 👉 **Python 3.12 と pyjq の非互換**

ログより：

```text
build/lib.linux-x86_64-cpython-312
DeprecationWarning: lib2to3 ...
```

* pyjq は **古い C 拡張**
* Python 3.12 では **ビルド不可**
* → 環境を直しても **pyjq 自体が入らない**

---

## 6️⃣ 現実的な解決策

### ✅ 解決策①（最推奨）

**Python 3.11 + venv で pyjq を使う**

```bash
sudo apt install -y python3.11 python3.11-venv python3.11-dev

cd /mnt/c/work/linux
rm -rf venv
python3.11 -m venv venv
source venv/bin/activate
pip install pyjq
```

---

### ✅ 解決策②（安定・おすすめ代替）

**jq を subprocess で使う（Python 3.12 OK）**

```bash
sudo apt install -y jq
```

```python
import subprocess, json

data = {"a": 1}
res = subprocess.run(
    ["jq", ".a"],
    input=json.dumps(data),
    text=True,
    capture_output=True
)
print(res.stdout)
```

---

### ⚠️ 非推奨

```bash
pip install pyjq --break-system-packages
```

* OS Python を壊す可能性あり

---

## 7️⃣ 学んだ重要ポイント

* Ubuntu では **pip は venv 前提**
* `(venv)` 表示だけでは信用しない
* `which python` / `which pip` が全て
* venv が壊れたら **必ず削除して作り直す**
* **pyjq は Python 3.12 非対応**

---

## 8️⃣ 結論（短く）

* **今の Python 3.12 では pyjq は無理**
* 使うなら：

  * **Python 3.11 に下げる**
  * or **jq を subprocess で使う**
