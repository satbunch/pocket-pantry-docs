---
tags: 
created: 2025-08-05 11:08
updated: 2025-08-07 14:35
---
# PocketPantry API仕様書（MVP版）

## 共通事項

* API形式：REST
* 認証：Supabase Auth（Bearer Token）
* レスポンス形式：JSON

---

## 1. Items API（在庫管理）
### 1.1 GET /items
* 説明：在庫一覧を取得
* リクエストパラメータ：
  * storage\_category（オプション：冷蔵／冷凍／常温／野菜室）
  * item\_status（オプション：in\_stock / low / out）
* レスポンス：
	 なお、レスポンスはマスタ結合済み文字列を返却（unit）

```json
[
  {
    "id": "uuid",
    "name": "卵",
    "storage_category": "冷蔵",
    "quantity": 10,
    "unit": "個",
    "item_status": "in_stock",
    "is_expiry_managed": true,
    "expiry_date": "2025-08-10",
    "jan_code": "4901234567890",
    "memo": "使いかけ",
    "updated_at": "2025-08-04T12:00:00Z",
    "created_at": "2025-08-04T12:00:00Z"
  }
]
```

### 1.2 POST /items

* 説明：食材を新規登録
* リクエストボディ：

```json
{
  "name": "牛乳",
  "storage_category": "冷蔵",
  "quantity": 1,
  "unit_id": "uuid",
  "item_status": "in_stock",
  "is_expiry_managed": true,
  "expiry_date": "2025-08-10",
  "jan_code": "4901234567890",
  "memo": ""
}
```

* レスポンス：201 Created + 登録内容

### 1.3 PATCH /items/{id}

* 説明：食材情報を更新
* リクエストボディ：部分更新可能

```json
{
  "quantity": 2,
  "item_status": "low",
  "memo": "あと少し"
}
```

* レスポンス：200 OK + 更新後データ

### 1.4 DELETE /items/{id}

* 説明：食材を削除
* レスポンス：204 No Content

---

## 2. Shopping Lists API
### 2.1 GET /shopping\_lists
* 説明：買い物リスト一覧を取得
* レスポンス：

```json
[
  {
    "id": "uuid",
    "name": "今週の買い物",
    "created_at": "2025-08-04T12:00:00Z"
  }
]
```

### 2.2 POST /shopping\_lists

* 説明：新しい買い物リストを作成
* リクエストボディ：

```json
{
  "name": "来週のまとめ買い"
}
```

* レスポンス：201 Created + リスト情報
### 2.3 DELETE /shopping\_lists/{id}
* 説明：買い物リストを削除
* レスポンス：204 No Content

---

## 3. Shopping List Items API

### 3.1 GET /shopping\_lists/{id}/items

* 説明：指定した買い物リストの明細を取得
* レスポンス：

```json
[
  {
    "id": "uuid",
    "item_id": "uuid",
    "custom_name": null,
    "quantity": 2,
    "is_checked": false
  },
  {
    "id": "uuid",
    "item_id": null,
    "custom_name": "ヨーグルト",
    "quantity": 1,
    "is_checked": true
  }
]
```

### 3.2 POST /shopping\_lists/{id}/items

* 説明：リストに食材を追加
* リクエストボディ：

```json
{
  "item_id": "uuid", // 既存itemsから選ぶ場合
  "custom_name": null, // 新規自由入力の場合はitem_idをnullにし、custom_nameに入力値
  "quantity": 1
}
```

* レスポンス：201 Created + 登録内容

### 3.3 PATCH /shopping\_list\_items/{id}

* 説明：リスト明細を更新
* リクエストボディ：

```json
{
  "quantity": 3,
  "is_checked": true
}
```

* レスポンス：200 OK + 更新後データ

### 3.4 DELETE /shopping\_list\_items/{id}

* 説明：リスト明細を削除
* レスポンス：204 No Content

---

## 4. Families API

### 4.1 GET /families/invite/{invite\_code}

* 説明：招待コードからファミリー情報を取得（参加時に使用）
* レスポンス：

```json
{
  "id": "uuid",
  "name": "里見家"
}
```

### 4.2 POST /profiles/join\_family

* 説明：ユーザーがファミリーに参加
* リクエストボディ：

```json
{
  "family_id": "uuid"
}
```

* レスポンス：200 OK

---

## 5. Usage Logs API

### 5.1 POST /usage\_logs

* 説明：OCRやバーコード使用時のログ記録
* リクエストボディ：

```json
{
  "action_type": "ocr_scan",
  "parameter_json": {
    "ocr_confidence": 0.92
  }
}
```

* レスポンス：201 Created

```
```
