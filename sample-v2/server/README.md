# PAY.JP Checkout V2 サンプルサーバー

PAY.JP Checkout V2 を使用した決済のサンプルサーバーです。

## 前提条件

- Node.js 18 以上
- PAY.JP アカウント（テスト用 API キー）
- PAY.JP ダッシュボードで Price オブジェクトを作成済み

## セットアップ

### 1. 依存関係をインストール

```bash
cd sample-v2/server
npm install
```

### 2. 環境変数を設定

```bash
cp .env.example .env
```

`.env` ファイルを編集して PAY.JP の秘密鍵を設定します：

```
PAYJP_SECRET_KEY=sk_test_xxxxx
```

### 3. Price オブジェクトを作成

PAY.JP ダッシュボードまたは API で Price オブジェクトを作成します。
作成した Price ID（`price_xxx`）を `.env` の `PAYJP_SAMPLE_PRICE_ID` に設定してください。
商品名と金額はサンプル用に任意で設定できます。

### 4. サーバーを起動

```bash
npm start
```

サーバーが `http://localhost:3000` で起動します。

### 5. Webhook をローカルに転送（開発時）

PAY.JP CLI を使用して Webhook をローカルサーバーに転送します：

```bash
payjp-cli listen --forward-to http://localhost:3000/webhook
```

## エンドポイント

### GET /products

サンプル商品一覧を返します。

**レスポンス:**
```json
{
  "products": [
    { "id": "price_xxx", "name": "テスト商品A", "amount": 100 }
  ]
}
```

### POST /create-checkout-session

Checkout Session を作成します。

**リクエスト:**
```json
{
  "price_id": "price_xxx",
  "quantity": 1,
  "success_url": "payjpcheckoutexample://checkout/success",
  "cancel_url": "payjpcheckoutexample://checkout/cancel"
}
```

**レスポンス:**
```json
{
  "id": "cs_xxx",
  "url": "https://checkout.pay.jp/...",
  "status": "open"
}
```

### POST /webhook

PAY.JP からの Webhook を受信します。

**処理するイベント:**
- `checkout.session.completed` - 決済完了
- `checkout.session.expired` - セッション期限切れ

## 注意事項

### Webhook の重要性

`success_url` へのリダイレクトは決済完了を保証しません。
必ず Webhook で `checkout.session.completed` イベントを確認してから商品を発送してください。

### セキュリティ

- 秘密鍵（`sk_xxx`）は絶対にクライアントサイドに含めないでください
- Webhook シークレットを設定して、不正なリクエストを防いでください

## テスト

### テストカード

- カード番号: `4242 4242 4242 4242`
- 有効期限: 未来の日付
- CVC: 任意の3桁

### PayPay テストアカウント

- 電話番号: `08011115912`
- パスワード: `Pay2test`
- パスコード: `1234`
- **注意**: 100円以下でテストし、必ず返金してください

## 参考リンク

- [PAY.JP Checkout V2 ガイド](https://docs.pay.jp/v2/guide/payments/checkout)
- [PAY.JP API リファレンス](https://docs.pay.jp/v2/api)
- [PAY.JP CLI](https://docs.pay.jp/v2/guide/developers/payjp-cli)
