---
title: "Web StorageとCookieの仕様を比較する"
emoji: "🗃"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["web","browser","storage","javascript","html5"]
published: false
---

# Web Storageとは

Web Storageは、ユーザーのローカル環境のブラウザにデータを保存するための仕組みです。
データの追加・更新・削除などの操作はJavaScript上で行うことができます。
以下はCookieとWeb Storage(Local Storage, Session Storage)の比較表です。

| 種類              | 別ウィンドウでのデータ共有 | データ容量 | 有効期限             | サーバーへのデータ送信 |
|-----------------|---------------|-------|------------------|--|
| cookie          | ○             | 4KB   | 指定期限まで有効[^1]     | サーバーへアクセスするたびに毎回自動送信 |
| local storage   | ○             | 1オリジン当たり5MB | 保存期間の期限なし[^2]    | 必要時のみスクリプトやフォームなどで送信 |
| session storage | ×             | 1オリジン当たり5MB | ウィンドウやタブを閉じるまで有効 | 必要時のみスクリプトやフォームなどで送信 |

## Cookie

`Set-Cookie`はHTTPのレスポンスヘッダーで、サーバーからユーザーエージェントへクッキーを送信するために使われる。
また、ユーザーエージェントはサーバーに送り返すことができる。
そのため、HTTP サーバーが HTTP ユーザーエージェントに状態を保存するために使用することができる。

### Cookieの利用目的

- セッション管理 
  - ログイン状態や買い物時のカートの状態など
- パーソナライズ
- トラッキング

### Set-CookieとCookieヘッダ

HTTP の Set-Cookie レスポンスヘッダーは、サーバーがユーザーエージェントへ Cookie を送信するために使用します。

```http
HTTP/2.0 200 OK
Content-Type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry

[ページの内容]
```

そのサーバーへのその後のすべてのリクエストにおいて、ブラウザーは以前格納されたすべての Cookie を、 Cookie ヘッダーを使用してサーバーへ送信します。

```
GET /sample_page.html HTTP/2.0
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```

### Cookieのアクセス制限

Cookie が安全に送信され、意図しない第三者やスクリプトからアクセスされないようにするには、 Secure 属性と HttpOnly 属性の 2 つの方法があります。

![](/images/cookie.png)

Secure 属性がついた Cookie は HTTPS プロトコル上の暗号化されたリクエストでのみサーバーに送信され、安全でない HTTP では決して送信されないため、中間者攻撃者が簡単にアクセスすることはできません。
HttpOnly 属性を持つ Cookie は、 JavaScript の Document.cookie API にはアクセスできません。サーバーに送信されるだけです。例えば、サーバー側のセッションを持続させる Cookie は JavaScript が利用する必要はないので、 HttpOnly 属性をつけるべきです。

# 参考

- [MDN web dock: Window.localStorage](https://developer.mozilla.org/ja/docs/Web/API/Window/localStorage)
- [HTML Living Standard: Web storage](https://html.spec.whatwg.org/multipage/webstorage.html#dom-localstorage-dev)
- [MDN web dock: HTTP Cookie の使用](https://developer.mozilla.org/ja/docs/Web/HTTP/Cookies)
- [MDN web dock: Set-Cookie](https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/Set-Cookie)
- [The IETF HTTP Working Group Documentation: HTTP State Management Mechanism](https://httpwg.org/specs/rfc6265.html)

***

[^1]: expiresに指定した期限が適用される。設定された日時はサーバーではなく、`Cookie`が設定されるクライアントの日時に関連する。
[^2]: ユーザエージェントは、場合によってはユーザによって設定された方法で、一定期間後に保存データを自動的に削除することができる。
