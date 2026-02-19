# Gemini 2.5 Flash Image（`gemini-2.5-flash-image`）API仕様

## モデル識別子

### Gemini API（Google AI for Developers）
|項目|値|
|---|---|
|モデルコード|`gemini-2.5-flash-image`|
|（参考）プレビュー|`gemini-2.5-flash-image-preview`（モデルページにサポート終了情報あり）|
|入力|画像・テキスト|
|出力|画像・テキスト|
|入力トークン上限|65,536|
|出力トークン上限|32,768|
|画像生成|サポート|
|Batch API|サポート|
|キャッシュ保存|サポート|
|構造化出力|サポート|
|検索によるグラウンディング|サポート対象外|
|思考モード|サポート対象外|
|関数呼び出し|サポート対象外|
|コードの実行|サポート対象外|
|Live API|サポート対象外|
|URL コンテキスト|サポート対象外|

### Vertex AI（Generative AI on Vertex AI）
|項目|値|
|---|---|
|モデルID|`gemini-2.5-flash-image`|
|入力|Text, Images|
|出力|Text and image|
|最大入力トークン|32,768|
|最大出力トークン|32,768|
|画像生成（トークン消費）|生成画像 1 枚あたり 1290 トークン|

## 画像生成の出力仕様（Gemini API / Vertex）

### アスペクト比・解像度・トークン（Gemini 2.5 Flash Image）
|アスペクト比|解像度|トークン（1枚）|
|---|---:|---:|
|1:1|1024×1024|1290|
|2:3|832×1248|1290|
|3:2|1248×832|1290|
|3:4|864×1184|1290|
|4:3|1184×864|1290|
|4:5|896×1152|1290|
|5:4|1152×896|1290|
|9:16|768×1344|1290|
|16:9|1344×768|1290|
|21:9|1536×672|1290|

## 画像生成パラメータ（GenerationConfig.imageConfig）

`generationConfig.imageConfig` の主要フィールド（Vertex AI v1beta1 の型定義）:
- `aspectRatio`（string）: `"1:1"`, `"2:3"`, `"3:2"`, `"3:4"`, `"4:3"`, `"4:5"`, `"5:4"`, `"9:16"`, `"16:9"`, `"21:9"`
- `personGeneration`（enum）: `PERSON_GENERATION_UNSPECIFIED`, `ALLOW_ALL`, `ALLOW_ADULT`, `ALLOW_NONE`
- `imageSize`（string）: `1K`, `2K`, `4K`（未指定時は `1K`）
- `imageOutputOptions`（object）: `mimeType`（string）, `compressionQuality`（integer）


## Gemini API（Google AI for Developers）: REST 呼び出し

### エンドポイント
```
POST https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-image:generateContent
```

### ヘッダ
```
x-goog-api-key: <GEMINI_API_KEY>
Content-Type: application/json
```

### 最小リクエスト例（画像生成）
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
      "aspectRatio": "16:9"
    }
  }
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
projects/{project}/locations/{location}/publishers/google/models/gemini-2.5-flash-image
```

### リクエストボディ（主要フィールド）
- `contents[]`（必須）: 会話履歴＋最新リクエスト（単発は 1 要素）
- `tools[]`（任意）: モデルが使用可能なツール
- `toolConfig`（任意）
- `generationConfig`（任意）: 生成パラメータ（`imageConfig` を含む）
- `cachedContent`（任意）: 明示的キャッシュの参照
- `labels`（任意）: 課金・レポート用メタデータ

## Vertex AI の技術仕様（画像）
- 1プロンプトあたり最大画像数: 3
- 1ファイル最大サイズ（inline / コンソール直接アップロード）: 7 MB
- 1ファイル最大サイズ（Cloud Storage）: 30 MB
- 1プロンプトあたり最大出力画像数: 10
- 対応アスペクト比: 1:1, 3:2, 2:3, 3:4, 4:3, 4:5, 5:4, 9:16, 16:9, 21:9
- 対応 MIME: `image/png`, `image/jpeg`, `image/webp`, `image/heic`, `image/heif`

## 参考資料（一次情報）
- Gemini API モデル: Gemini 2.5 Flash Image（日本語）  
  https://ai.google.dev/gemini-api/docs/models/gemini-2.5-flash-image?hl=ja
- Gemini API 画像生成ガイド（Nano Banana image generation）  
  https://ai.google.dev/gemini-api/docs/image-generation
- Vertex AI モデル: Gemini 2.5 Flash Image  
  https://docs.cloud.google.com/vertex-ai/generative-ai/docs/models/gemini/2-5-flash-image
- Vertex AI REST: `GenerationConfig`（v1beta1）  
  https://docs.cloud.google.com/vertex-ai/generative-ai/docs/reference/rest/v1beta1/GenerationConfig
- Vertex AI REST: `projects.locations.publishers.models.generateContent`  
  https://docs.cloud.google.com/vertex-ai/generative-ai/docs/reference/rest/v1/projects.locations.publishers.models/generateContent
