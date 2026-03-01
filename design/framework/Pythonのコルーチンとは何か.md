# Pythonにおける coroutine（コルーチン）とは

Pythonにおける **coroutine（コルーチン）** とは、  
**途中で処理を中断・再開できる関数** のことで、主に **非同期処理（async / await）** で使われます。

---

## 1. coroutineの基本概念

### 通常の関数
- 上から下まで一気に実行される
- 呼び出し元に戻るのは処理が終了したときのみ

### coroutine
- `await` の位置で処理を一時停止できる
- 他の処理に制御を譲ることができる
- 再開すると停止した続きから実行される

---

## 2. Pythonでのcoroutineの定義

Pythonでは **`async def`** を使って定義します。

```python
async def hello():
    print("Hello")
    await asyncio.sleep(1)
    print("World")
