# Gemini 3 Pro Image（`gemini-3-pro-image-preview`）API仕様

## モデル識別子

### Gemini API（Google AI for Developers）
|項目|値|
|---|---|
|モデルコード|`gemini-3-pro-image-preview`|
|入力|画像・テキスト|
|出力|画像・テキスト|
|入力トークン上限|65,536|
|出力トークン上限|32,768|
|画像生成|サポート|
|Batch API|サポート|
|キャッシュ保存|サポート対象外|
|検索によるグラウンディング|サポート|
|思考モード|サポート|
|構造化出力|サポート|
|関数呼び出し|サポート対象外|
|コードの実行|サポート対象外|
|Live API|サポート対象外|
|URL コンテキスト|サポート対象外|

### Vertex AI（Generative AI on Vertex AI）
|項目|値|
|---|---|
|モデルID|`gemini-3-pro-image-preview`|
|入力|Text, Images|
|出力|Text and image|
|最大入力トークン|65,536|
|最大出力トークン|32,768|

## 画像生成の出力仕様（Gemini API）

### 既定解像度と `imageSize`
- 既定の出力は 1K。
- 2K / 4K を指定する場合、`generationConfig.imageConfig.imageSize` を使用する。
- `imageSize` の値は `1K`, `2K`, `4K`（大文字の K が必須。小文字は拒否される）。

### アスペクト比・解像度・トークン（Gemini 3 Pro Image Preview）
|アスペクト比|1K 解像度|1K トークン（1枚）|2K 解像度|2K トークン（1枚）|4K 解像度|4K トークン（1枚）|
|---|---:|---:|---:|---:|---:|---:|
|1:1|1024×1024|1120|2048×2048|1120|4096×4096|2000|
|2:3|848×1264|1120|1696×2528|1120|3392×5056|2000|
|3:2|1264×848|1120|2528×1696|1120|5056×3392|2000|
|3:4|896×1200|1120|1792×2400|1120|3584×4800|2000|
|4:3|1200×896|1120|2400×1792|1120|4800×3584|2000|
|4:5|928×1152|1120|1856×2304|1120|3712×4608|2000|
|5:4|1152×928|1120|2304×1856|1120|4608×3712|2000|
|9:16|768×1376|1120|1536×2752|1120|3072×5504|2000|
|16:9|1376×768|1120|2752×1536|1120|5504×3072|2000|
|21:9|1584×672|1120|3168×1344|1120|6336×2688|2000|

## 画像生成パラメータ（GenerationConfig.imageConfig）

`generationConfig.imageConfig` の主要フィールド（Vertex AI v1beta1 の型定義）:
- `imageOutputOptions`（object）: 出力形式（`mimeType`）と圧縮品質（`compressionQuality`）
- `aspectRatio`（string）: `"1:1"`, `"2:3"`, `"3:2"`, `"3:4"`, `"4:3"`, `"4:5"`, `"5:4"`, `"9:16"`, `"16:9"`, `"21:9"`
- `personGeneration`（enum）: `PERSON_GENERATION_UNSPECIFIED`, `ALLOW_ALL`, `ALLOW_ADULT`, `ALLOW_NONE`
- `imageSize`（string）: `1K`, `2K`, `4K`（未指定時は `1K`）

## Gemini API（Google AI for Developers）: REST 呼び出し

### エンドポイント
```
POST https://generativelanguage.googleapis.com/v1beta/models/gemini-3-pro-image-preview:generateContent
```

### ヘッダ
```
x-goog-api-key: <GEMINI_API_KEY>
Content-Type: application/json
```

### 最小リクエスト例（2K 画像生成）
```json
{
  "contents": [
    {
      "parts": [
        { "text": "Create a picture of a nano banana dish in a fancy restaurant with a Gemini theme" }
      ]
    }
  ],
  "generationConfig": {
    "imageConfig": {
      "aspectRatio": "16:9",
      "imageSize": "2K"
    }
  }
}
```

### 検索によるグラウンディング（Google Search ツール）
`tools` に `google_search` を指定する例:
```json
{
  "tools": [{ "google_search": {} }]
}
```

### 画像入力（画像編集・参照画像など）
`parts[]` に `inline_data` を含める形式の例（base64 文字列を指定）:
```json
{
  "inline_data": {
    "mime_type": "image/png",
    "data": "<BASE64_IMAGE_DATA>"
  }
}
```

## Vertex AI: REST 呼び出し（generateContent）

### エンドポイント
```
POST https://aiplatform.googleapis.com/v1/{model}:generateContent
```

### パスパラメータ
`model`（必須）: パブリッシャーモデルの完全修飾名、またはチューニング済みモデルエンドポイント  
- パブリッシャーモデル形式: `projects/{project}/locations/{location}/publishers/*/models/*`
- チューニング済みモデル形式: `projects/{project}/locations/{location}/endpoints/{endpoint}`

Google 提供モデルを指定する例:
```
projects/{project}/locations/{location}/publishers/google/models/gemini-3-pro-image-preview
```

### リクエストボディ（主要フィールド）
- `contents[]`（必須）: 会話履歴＋最新リクエスト（単発は 1 要素）
- `tools[]`（任意）: モデルが使用可能なツール
- `toolConfig`（任意）
- `generationConfig`（任意）: 生成パラメータ（`imageConfig` を含む）
- `cachedContent`（任意）: 明示的キャッシュの参照
- `labels`（任意）: 課金・レポート用メタデータ

## Vertex AI の技術仕様（画像）
- 1プロンプトあたり最大画像数: 14
- 1ファイル最大サイズ（inline / コンソール直接アップロード）: 7 MB
- 1ファイル最大サイズ（Cloud Storage）: 30 MB
- 1プロンプトあたり最大出力画像数: 出力トークン 32,768 によって制限
- 対応アスペクト比: 1:1, 3:2, 2:3, 3:4, 4:3, 4:5, 5:4, 9:16, 16:9, 21:9
- 対応 MIME: `image/png`, `image/jpeg`, `image/webp`, `image/heic`, `image/heif`

## 参考資料（一次情報）
- Gemini API モデル: Gemini 3 Pro 画像プレビュー（日本語）  
  https://ai.google.dev/gemini-api/docs/models/gemini-3-pro-image-preview?hl=ja
- Gemini API 画像生成ガイド（Nano Banana image generation）  
  https://ai.google.dev/gemini-api/docs/image-generation
- Vertex AI モデル: Gemini 3 Pro Image  
  https://docs.cloud.google.com/vertex-ai/generative-ai/docs/models/gemini/3-pro-image
- Vertex AI REST: `GenerationConfig`（v1beta1）  
  https://docs.cloud.google.com/vertex-ai/generative-ai/docs/reference/rest/v1beta1/GenerationConfig
- Vertex AI REST: `projects.locations.publishers.models.generateContent`  
  https://docs.cloud.google.com/vertex-ai/generative-ai/docs/reference/rest/v1/projects.locations.publishers.models/generateContent
