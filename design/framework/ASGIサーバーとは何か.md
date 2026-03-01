## ASGIサーバーとは

ASGIサーバーとは、PythonのWebアプリケーションを動かすためのサーバーで、  
**ASGI（Asynchronous Server Gateway Interface）** という規格に対応したものです。

---

## ASGIとは？

ASGIは、従来の **WSGI** を拡張した新しいインターフェース規格で、  
以下のような機能を扱えるようにしたものです。

- 非同期処理（`async / await`）
- WebSocket
- 長時間接続（リアルタイム通信）

---

## まとめ

**「非同期・リアルタイム通信に対応した  
Python Webアプリの共通ルール」**

<details><summary>ASGIサーバーの処理フロー（クリックで展開）</summary>

```mermaid
flowchart TD
    A[クライアント] --> B[ASGIサーバー]

    B -->|HTTP| C[HTTPハンドラ]
    B -->|WebSocket| D[WebSocketハンドラ]

    C --> E[同期処理]
    C --> F[非同期処理]

    D --> G[接続確立]
    G --> H[双方向通信]
    H --> I[切断]
```
</details>