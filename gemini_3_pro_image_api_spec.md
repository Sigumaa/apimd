# Gemini 3 Pro Image API 仕様まとめ（Gemini API / Vertex AI）

この文書は、Gemini 3 Pro Image（モデルID: `gemini-3-pro-image-preview`）を使った **画像生成・画像編集** を実装するために必要なAPI仕様を、実装観点で整理したものです。  
Web検索なしの環境でも読めるように、リクエスト形式・レスポンス形式・落とし穴（特に思考署名）まで含めています。

---

## 1. モデル概要（Gemini 3 Pro Image）

Vertex AI 側のモデルカードでは、Gemini 3 Pro Image は次のように定義されています。

- モデルID: `gemini-3-pro-image-preview`（Public preview）
- 入力: Text, Images
- 出力: Text and image
- トークン上限: 入力 65,536 / 出力 32,768
- 入力サイズ上限: 500MB
- 1プロンプトあたり最大画像数: 14
- インライン/コンソール直アップロードの1ファイル最大: 7MB、GCS参照は 30MB
- 対応アスペクト比: `1:1`, `3:2`, `2:3`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`, `21:9`
- 対応MIME: `image/png`, `image/jpeg`, `image/webp`, `image/heic`, `image/heif`
- 対応機能: Google Searchグラウンディング、System instructions、Count Tokens、Thinking
- 非対応機能: Code execution、Function calling、Gemini Live API、（明示/暗黙）コンテキストキャッシュ、Vertex AI RAG Engine、Chat completions

また Vertex AI のガイドでは、画像出力は `gemini-3-pro-image-preview` と `gemini-2.5-flash-image` でのみサポートされ、`gemini-3-pro-preview`（テキスト/マルチモーダル）自体は画像出力をサポートしない点が明記されています。

---

## 2. どのAPI面で呼ぶか（Gemini API と Vertex AI）

同じモデルIDでも、呼び出し面が2系統あります。

### 2.1 Gemini API（Google AI for Developers / Generative Language API）

- ホスト: `generativelanguage.googleapis.com`
- 認証: APIキー（`x-goog-api-key` ヘッダ or `?key=`）
- 主要メソッド: `models.generateContent`（同期）、`models.streamGenerateContent`（SSE）

画像生成・編集の最短ルートは、Gemini API で `generateContent` を叩く形です。

### 2.2 Vertex AI（Generative AI on Vertex AI）

- 認証: OAuth2（Bearerアクセストークン。ADCやサービスアカウント、`gcloud auth print-access-token` など）
- 主要メソッド: `:generateContent`
- ドキュメントのRESTサンプルでは `https://${API_ENDPOINT}:generateContent` の形で表現されています（`API_ENDPOINT` はモデルリソースを指す文字列）

注意: Vertex のサンプルは JSON のフィールド名が一部 camelCase（例: `fileData`, `mimeType`）で表記されています。一方、Gemini API（Generative Language API）の REST 参照は snake_case（例: `file_data`, `mime_type`）が基本です。SDK（Node/Java/Python）を使う場合は、SDKが期待する命名に従います。

---

## 3. Gemini API（Generative Language API）: エンドポイント仕様（REST）

### 3.1 generateContent（同期）

- HTTP: `POST`
- URL: `https://generativelanguage.googleapis.com/v1beta/models/{model}:generateContent`
- `{model}` は `gemini-3-pro-image-preview` を指定（例: `models/gemini-3-pro-image-preview` ではなく、URL上は `.../models/gemini-3-pro-image-preview:generateContent`）
- 認証:
  - ヘッダ: `x-goog-api-key: <GEMINI_API_KEY>`
  - もしくはクエリ: `?key=<GEMINI_API_KEY>`

### 3.2 streamGenerateContent（SSE）

- HTTP: `POST`
- URL: `https://generativelanguage.googleapis.com/v1beta/models/{model}:streamGenerateContent?alt=sse`
- 主にテキストのストリーミング用途。画像を含む応答は、実装上は最後にまとまって返ることが多いため、クライアントはSSEイベントの終端処理を確実に行うこと。

---

## 4. リクエスト構造（共通概念: Content と Part）

`generateContent` の入力は、会話の履歴を表す `contents` 配列（単発なら要素1つ）です。

- `contents[]`: `Content`
  - `role`: `"user"` / `"model"`（単発でも指定可能。省略例もある）
  - `parts[]`: `Part` の配列
- `Part` の主な形（画像生成/編集で重要なもの）
  - `{"text": "..."}`
  - `{"inline_data": {"mime_type": "image/png", "data": "<base64>"}}`
  - `{"file_data": {"mime_type": "image/png", "file_uri": "<files api uri など>"}}`

---

## 5. 画像生成（テキストのみ → 画像）

### 5.1 最小構成（画像のみを返す）

`generationConfig.responseModalities` を `["Image"]` または `["IMAGE"]` とする例がドキュメントにあります。実装では大文字の `IMAGE` を推奨します。

```bash
curl -s -X POST \
  "https://generativelanguage.googleapis.com/v1beta/models/gemini-3-pro-image-preview:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [
        {"text": "A cute Shiba Inu wearing a blue scarf, studio photo, high detail"}
      ]
    }],
    "generationConfig": {
      "responseModalities": ["IMAGE"]
    }
  }'
```

### 5.2 画像とテキストを混ぜて返す（インターリーブ）

説明文と画像を交互に返してほしい場合は、`["TEXT","IMAGE"]` を指定します。

```json
{
  "contents": [
    {
      "parts": [
        {"text": "Write a short travel blurb about Kyoto and include one image."}
      ]
    }
  ],
  "generationConfig": {
    "responseModalities": ["TEXT", "IMAGE"]
  }
}
```

---

## 6. 画像編集（入力画像 + テキスト指示 → 編集画像）

画像を `inline_data`（base64）または `file_data`（Files APIのURIなど）で渡し、同じ `generateContent` を使います。

### 6.1 inline_data で編集

```json
{
  "contents": [
    {
      "parts": [
        {
          "inline_data": {
            "mime_type": "image/png",
            "data": "<BASE64_ENCODED_IMAGE_BYTES>"
          }
        },
        {
          "text": "Convert this to black and white, and make it look like a cartoon."
        }
      ]
    }
  ],
  "generationConfig": {
    "responseModalities": ["TEXT", "IMAGE"]
  }
}
```

### 6.2 file_data で編集（Files APIと併用）

```json
{
  "contents": [
    {
      "parts": [
        {
          "file_data": {
            "mime_type": "image/png",
            "file_uri": "files/...."
          }
        },
        {
          "text": "Replace the background with a sunny beach."
        }
      ]
    }
  ]
}
```

---

## 7. 画像出力の制御（aspectRatio と imageSize）

### 7.1 アスペクト比（aspectRatio）

`generationConfig.imageConfig.aspectRatio` で指定します。  
対応比率は次のいずれかです。

`1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`, `21:9`

例:

```json
{
  "generationConfig": {
    "responseModalities": ["IMAGE"],
    "imageConfig": {
      "aspectRatio": "16:9"
    }
  }
}
```

### 7.2 解像度（imageSize）

`gemini-3-pro-image-preview` は `imageSize` を指定できます（ドキュメント例あり）。  
指定値は `1K`, `2K`, `4K` で、`K` を小文字にすると拒否される旨が注意書きとして示されています。

例:

```json
{
  "generationConfig": {
    "responseModalities": ["IMAGE"],
    "imageConfig": {
      "aspectRatio": "16:9",
      "imageSize": "4K"
    }
  }
}
```

Vertex AI のガイドでは、Gemini 3 Pro Image は最大 4096px の画像生成に対応すると説明されています。

---

## 8. レスポンス構造（画像の取り出し方）

`generateContent` のレスポンスは `candidates[]` を返し、通常は `candidates[0].content.parts[]` を順に走査して、テキストと画像を拾います。

Gemini API（REST）の場合、画像は `inline_data.data` に base64 文字列として返るのが基本です。  
保存する場合は base64 をデコードしてバイト列に戻します。

擬似コード（言語非依存）:

```text
for part in response.candidates[0].content.parts:
  if part.text:
    append_to_markdown(part.text)
  if part.inline_data:
    bytes = base64_decode(part.inline_data.data)
    write_file("out.png", bytes)
```

---

## 9. 思考（Thinking）と thought_signature（重要）

Gemini 3 Pro Image は thinking model として扱われます。Gemini API の画像生成ドキュメントでは、思考はデフォルトで有効で無効化できない、とされています。

また、multi-turn（会話を続けて編集するなど）では、レスポンスに含まれる `thought_signature` を次のリクエストに含める必要があります。  
これを落とすと、次ターンの生成が失敗する可能性があるため、履歴を保存する実装では以下を推奨します。

- 1ターン目のレスポンス（`candidates[0].content`）を、`role: "model"` の `Content` として **丸ごと履歴に保存**
- 2ターン目以降は `contents` に履歴をすべて含めて送信（その中に `thought_signature` も含まれる）

実務的には、過去ターンの `Content.parts` からテキストだけ抜いて再構成しないことが安全です（思考署名を落としやすい）。

---

## 10. Files API（大きい入力・再利用する入力向け）

Gemini API には `Files API` があり、ファイルをアップロードして `file_uri` 参照で入力に使えます。  
大きめの入力や、multi-turn で同じ画像を何度も参照する場合に向きます。

### 10.1 アップロード（resumable）概要

1) メタデータ付きでアップロード開始 → `x-goog-upload-url` を取得  
2) そのURLへ実バイトを送って finalize  
3) 返ってきた `file.uri` を `file_data.file_uri` として使う

Files API のドキュメントでは、アップロード例が `POST {BASE_URL}/upload/v1beta/files`（`X-Goog-Upload-Protocol: resumable`）として示されています。

### 10.2 代表的な操作

- 一覧: `GET https://generativelanguage.googleapis.com/v1beta/files`
- メタデータ取得: `GET https://generativelanguage.googleapis.com/v1beta/files/{name}`
- 削除: `DELETE https://generativelanguage.googleapis.com/v1beta/files/{name}`（SDK例あり）

ファイルは自動的に 48時間後に削除される旨が Files API ドキュメントに記載されています。

---

## 11. Vertex AI 側（REST）での呼び出しメモ

Vertex AI のガイドのREST例は次の形です。

- 認証: `Authorization: Bearer $(gcloud auth print-access-token)`
- URL: `https://${API_ENDPOINT}:generateContent`
- リクエストJSONは `contents.role` に `"USER"`（大文字）を使う例が示されています

```bash
curl -X POST \
  -H "Authorization: Bearer $(gcloud auth print-access-token)" \
  -H "Content-Type: application/json" \
  https://${API_ENDPOINT}:generateContent \
  -d '{
    "contents": {
      "role": "USER",
      "parts": [
        {"text": "Generate an illustrated recipe for a paella and include images."}
      ]
    },
    "generationConfig": {
      "responseModalities": ["TEXT", "IMAGE"],
      "imageConfig": {"aspectRatio": "16:9"}
    }
  }'
```

補足: Vertex AI のドキュメントには `fileData` / `mimeType` / `fileUri` の表記例があります。実装では Vertex のSDKやサンプルに合わせてフィールド名を揃えてください。

---

## 12. 参照元（公式ドキュメント）

以下は、このメモを作るときに参照した公式ページです。URLはコードブロックに入れてあります。

```text
https://docs.cloud.google.com/vertex-ai/generative-ai/docs/models/gemini/3-pro-image
https://docs.cloud.google.com/vertex-ai/generative-ai/docs/multimodal/image-generation

https://ai.google.dev/gemini-api/docs/image-generation
https://ai.google.dev/api/generate-content
https://ai.google.dev/gemini-api/docs/files
https://ai.google.dev/gemini-api/docs/file-input-methods
```

---

## 付録: 実装チェックリスト（落とし穴回避）

- `responseModalities` を明示（画像だけなら `["IMAGE"]`、混在なら `["TEXT","IMAGE"]`）
- 画像編集の multi-turn では履歴を正確に保持（`thought_signature` を落とさない）
- `imageSize` は `1K/2K/4K` の `K` を大文字
- レスポンスの `parts[]` は順序が意味を持つため、順番どおりに処理する
- base64 デコード/エンコードの境界（RESTはbase64、SDKはbytes）を混同しない
