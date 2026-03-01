# Uvicornとは

**Uvicorn（ユーヴィコーン）** とは、  
Python向けの **高速なASGIサーバー** です。

主に **FastAPI** や **Starlette**、**Django（ASGI対応）** などの  
**非同期Webフレームワーク**を動かすために使われます。

---

## ざっくり一言で
👉 **「Pythonの非同期Webアプリを高速に動かすサーバー」**

---

## ASGIとは？
- **ASGI (Asynchronous Server Gateway Interface)**
- 従来の **WSGI（Flask / Django旧来）** の進化版
- 非同期処理（async / await）を前提にしている

👉 WebSocket、長時間接続、並列処理が得意

---

## Uvicornの特徴

### 🚀 高速
- **uvloop**（高速なイベントループ）
- **httptools**（高速HTTPパーサ）
を利用可能

### 🔁 非同期対応
- async / await をフル活用
- WebSocketやSSEも自然に扱える

### 🧩 FastAPIとの相性が最高
FastAPIの公式推奨サーバー

---

## よくある使い方（FastAPI）

```bash
pip install uvicorn fastapi
